# Regenerate Production configuration files

To regenerate config files for redis, supervisor, socketio, nginx after version 6 install / upgrade in Production Mode, run the following commands on your bench.

```shell
# Run command on frappe-bench directory
bench setup socketio
bench setup supervisor
bench setup nginx
bench setup redis
service nginx reload
supervisorctl reload
```