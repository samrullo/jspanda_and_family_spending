The containers are currently serving traffic, but `jspanda` has a serious process leak that is driving swap usage.

**Resource Snapshot**

| Container | CPU | Memory observed | Processes | Notes |
|---|---:|---:|---:|---|
| `jspanda` | 0.06–0.88% | 169–341 MiB | 37 | Abnormal uWSGI accumulation |
| `jspanda_postgres` | ~0% | 24–29 MiB | 24 | Healthy, but 18 idle app connections |
| `family_spending` | ~0% | 6–16 MiB | 4 | Normal |
| `family_spending_postgres` | ~0% | 17–18 MiB | 6 | Healthy |
| `nginx_proxy` | ~0% | ~5 MiB | 2 | Normal |
| `certbot` | ~0% | 4–7 MiB | 2 | Normal |

The host has 1 CPU and 985 MiB RAM. Approximately 2.6–2.8 GiB of its 4 GiB swap is occupied. CPU load is low, but occasional swap-ins are occurring, so memory pressure can cause intermittent latency.

**Critical Finding**

A cron entry runs this every day:

```cron
0 8 * * * docker exec jspanda systemctl restart jspanda
```

That restart does not terminate the previous uWSGI process. It has accumulated approximately one master/worker pair per day: 17 pairs after 17 days. Those processes also created 18 idle PostgreSQL connections.

The underlying image has an inherited entrypoint:

```text
ENTRYPOINT ["systemctl", "start", "nginx"]
```

Consequently, the Compose `command` at [`docker-compose.yml:18`](/Users/samrullo/programming/pyprojects/jspanda_and_family_spending/docker-compose.yml:18) is appended to that entrypoint rather than directly becoming PID 1. The container logs confirm that the fake `systemctl` wrapper cannot correctly resolve the requested uWSGI service.

Recommended correction:

- Override/remove the inherited entrypoint.
- Run `uwsgi --ini /var/www/jspanda_business/uwsgi.ini` directly as PID 1.
- Do the same for `family_spending`.
- Remove the daily `systemctl restart` cron entry.
- Recreate `jspanda` once the fix is deployed to clear the 17 stale process pairs and release swap gradually.

I did not perform this because it causes brief application downtime.

**Other Improvements**

1. Add `restart: unless-stopped`. All six containers currently have an empty restart policy, so they are not protected after crashes or host reboots.

2. Add memory and process limits. Every container currently has unlimited memory, CPU, and PIDs. After correcting the uWSGI leak, establish normal usage and add `mem_limit`, `pids_limit`, and health checks for the app containers.

3. Fix database readiness. Production only waits for the database container to start, not become healthy. Use `condition: service_healthy`, as your local Compose file already does.

4. Pin images. The deployed versions are PostgreSQL 16.0, Nginx 1.25.2, and Certbot 2.6.0, but the Dockerfiles use floating tags. Pin tested patch releases before rebuilding.

5. Reduce image sizes. Each application image is 1.93 GB. A slim Python runtime and direct uWSGI process would substantially reduce image size and remove the systemctl layer.

6. Rotate application logs. `jspanda_business/uwsgi.logs` is already 165 MB and `family-spending` is 34 MB. Send logs to stdout and configure Docker `json-file` rotation, for example `max-size: 10m` and `max-file: 3`.

7. Upgrade the host. It is running Ubuntu 18.04, kernel 4.15, Docker 20.10.7, and has been up for 395 days. Plan an OS upgrade and controlled reboot after verifying backups.

8. Repair the backup cron. `* * * * 0` means every minute on Sundays, not once per week. The script also contains a plaintext legacy MySQL password; rotate that credential and remove it from the script.

9. Complete certificate handling. The `jspanda.club` certificate is valid until September 16, 2026, and renewal is working. Nginx still needs an automatic reload after successful renewal. `samrullobusiness.club` remains HTTP-only.

Disk capacity is acceptable: the Docker volume is 53% used with roughly 45 GB available. No container has restarted or been OOM-killed since deployment.