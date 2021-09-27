# Bench Configuration

These configurations are utilised by the Bench CLI and may be set differently for each bench on your system through the common site configuration.

restart_supervisor_on_update	Restart supervisord on bench update
restart_systemd_on_update	Restart systemd on bench update
release_bench	If set, the current bench will be treated purely as a tool for performing releases.
background_workers	Number of workers allocated for handling the background jobs.
dns_multitenant	If set, turns on DNS multitenany for Bench. Used during NGINX conf generations.
file_watcher_port	Port number for the File Watcher
frappe_user	The user under which the current Bench was created.
gunicorn_workers	Number of Gunicorn workers. Default minimum value is 2.
maintenance_mode	If set, takes sites offline and stops all background processes and scheduled jobs. Used during site migrations and bench update operations.
pause_scheduler	If set, pauses the scheduler for the specified sites.
redis_cache	Port number for the redis cache process defined in the Bench Procfile.
redis_queue	Port number for the redis queue process defined in the Bench Procfile.
redis_socketio	Port number for the redis Socket.IO process defined in the Bench Procfile.
scheduler_tick_interval	Job Scheduler runs a loop that enqueues all scheduled jobs pending execution once every scheduler_tick_interval seconds. This makes it possible to set the minimum frequency for scheduling jobs. Defaults to 60.
socketio_port	Port number for the Socket.IO process defined in the Bench Procfile.
webserver_port	Port number for the Frappe web server.

## Integrations 
Some third party integrations may require lower level configurations you may want to define for all the sites on your bench, if you're a service provider. Frappe let's you define these config settings for your integrations via the site config.

## Sandbox 
sandbox_api_key	Sandbox API Key
sandbox_api_secret	Sandbox API Secret
sandbox_api_password	Sandbox API Password
sandbox_api_username	Sandbox API Username
sandbox_publishable_key	Sandbox Publishable Key
sandbox_signature	Sandbox Signature

## DropBox 
dropbox_access_key	Dropbox Access Key
dropbox_broker_site	Dropbox Broker Site
dropbox_secret_key	Dropbox Secret Key

## PayPal 
paypal_username	Paypal Username
paypal_password	PayPal Password
paypal_signature	Paypal Signature

## Others 
google_analytics_id	Google Analytics ID
mixpanel_id	Mixpanel ID for MixPanel analytics on desk
converted_rupee_to_paisa	Converted Rupee To Paisa for RazorPay Settings