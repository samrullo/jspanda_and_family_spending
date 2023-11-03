when installing uwsgi it fails with below error

```bash
  Running setup.py install for uwsgi ... error
  error: subprocess-exited-with-error

  × Running setup.py install for uwsgi did not run successfully.
  │ exit code: 1
  ╰─> [11 lines of output]
      /root/.pyenv/versions/3.9.14/lib/python3.9/distutils/dist.py:274: UserWarning: Unknown distribution option: 'descriptions'   
        warnings.warn(msg)
      running install
      using profile: buildconf/default.ini
      detected include path: ['/usr/lib/gcc/x86_64-linux-gnu/11/include', '/usr/local/include', '/usr/include/x86_64-linux-gnu', '/usr/include']
      Patching "bin_name" to properly install_scripts dir
      detected CPU cores: 1
      configured CFLAGS: -O2 -I. -Wall -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -Qunused-arguments -Wextra -Wno-unused-parameter -Wno-missing-field-initializers -DUWSGI_HAS_IFADDRS -DUWSGI_ZLIB -DUWSGI_LOCK_USE_MUTEX -DUWSGI_EVENT_USE_EPOLL -DUWSGI_EVENT_TIMER_USE_TIMERFD -DUWSGI_EVENT_FILEMONITOR_USE_INOTIFY -DUWSGI_UUID -DUWSGI_VERSION="\"2.0.22\"" -DUWSGI_VERSION_BASE="2" -DUWSGI_VERSION_MAJOR="0" -DUWSGI_VERSION_MINOR="22" -DUWSGI_VERSION_REVISION="0" -DUWSGI_VERSION_CUSTOM="\"\"" -DUWSGI_YAML -DUWSGI_SSL -I/usr/include/libxml2 -DUWSGI_XML -DUWSGI_XML_LIBXML2 -DUWSGI_PLUGIN_DIR="\".\"" -DUWSGI_DECLARE_EMBEDDED_PLUGINS="UDEP(python);UDEP(gevent);UDEP(ping);UDEP(cache);UDEP(nagios);UDEP(rrdtool);UDEP(carbon);UDEP(rpc);UDEP(corerouter);UDEP(fastrouter);UDEP(http);UDEP(ugreen);UDEP(signal);UDEP(syslog);UDEP(rsyslog);UDEP(logsocket);UDEP(router_uwsgi);UDEP(router_redirect);UDEP(router_basicauth);UDEP(zergpool);UDEP(redislog);UDEP(mongodblog);UDEP(router_rewrite);UDEP(router_http);UDEP(logfile);UDEP(router_cache);UDEP(rawrouter);UDEP(router_static);UDEP(sslrouter);UDEP(spooler);UDEP(cheaper_busyness);UDEP(symcall);UDEP(transformation_tofile);UDEP(transformation_gzip);UDEP(transformation_chunked);UDEP(transformation_offload);UDEP(router_memcached);UDEP(router_redis);UDEP(router_hash);UDEP(router_expires);UDEP(router_metrics);UDEP(transformation_template);UDEP(stats_pusher_socket);" -DUWSGI_LOAD_EMBEDDED_PLUGINS="ULEP(python);ULEP(gevent);ULEP(ping);ULEP(cache);ULEP(nagios);ULEP(rrdtool);ULEP(carbon);ULEP(rpc);ULEP(corerouter);ULEP(fastrouter);ULEP(http);ULEP(ugreen);ULEP(signal);ULEP(syslog);ULEP(rsyslog);ULEP(logsocket);ULEP(router_uwsgi);ULEP(router_redirect);ULEP(router_basicauth);ULEP(zergpool);ULEP(redislog);ULEP(mongodblog);ULEP(router_rewrite);ULEP(router_http);ULEP(logfile);ULEP(router_cache);ULEP(rawrouter);ULEP(router_static);ULEP(sslrouter);ULEP(spooler);ULEP(cheaper_busyness);ULEP(symcall);ULEP(transformation_tofile);ULEP(transformation_gzip);ULEP(transformation_chunked);ULEP(transformation_offload);ULEP(router_memcached);ULEP(router_redis);ULEP(router_hash);ULEP(router_expires);ULEP(router_metrics);ULEP(transformation_template);ULEP(stats_pusher_socket);"
      *** uWSGI compiling server core ***
      [gcc] core/utils.o
      gcc: error: unrecognized command-line option ‘-Qunused-arguments’
      [end of output]

  note: This error originates from a subprocess, and is likely not a problem with pip.
error: legacy-install-failure

× Encountered error while trying to install package.
╰─> uwsgi

note: This is an issue with the package mentioned above, not pip.
hint: See above for output from the failure.
WARNING: You are using pip version 22.0.4; however, version 23.2.1 is available.
You should consider upgrading via the '/root/.pyenv/versions/3.9.14/bin/python3.9 -m pip install --upgrade pip' command.
root@1615ca8b77d8:/var/www/jspanda_business#
```

Solution 

```bash
export CFLAGS=$(echo $CFLAGS | sed 's/-Qunused-arguments//g')
```