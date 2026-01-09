# Load Testing your Dokploy setup

This guide describes how to load test your Dokploy setup.

# Load Test Script

A very basic script to test the performance of your Dokploy setup. Make sure to disable stock tracking in global settings, to prevent out of stock errors during load test.

Each virtual user will execute the following steps:

* Fetch a product by slug
* Fetch the product's featured asset
* Adds the first variant to the active order
* Sets a shipping address
* Gets the eligible shipping methods and sets the first one

The script will run for X seconds, and adding Y new virtual users every second. More information on Artillery's concepts van be found [here](https://www.artillery.io/docs/get-started/core-concepts).


## Running the load test

1. `npm ci` in this directory.
2. Replace all the config values under `config:` in `load-test.yaml` with your own values.
3. Run `npx artillery run load-test.yaml` to run the load test.

If you are seeing more that 1 `vusers.failed` in the summary, you can debug the load test steps by running `DEBUG=http,http:request,http:response npx artillery run load-test.yaml` with `duration` and `arrivalRate` set to 1.
# Resource Isolation

We want to make sure that one Docker container can not crash the entire VPS, or hog all available CPU.

# Resource Isolation
The following sections describe how to smoke test if CPU and memory limits are working as expected.

## Memory Isolation
We are going to make the Vendure Worker consume all it's available memory, resulting in a crash of the container. We expect the container to be restarted automatically.

1. SSH in to your VPS and run `docker ps` to find the container ID of the Vendure Worker.
2. Run the following script to gradually ramp up memory usage until the container crashes.
```bash
docker exec -it <container_name_or_id> bash -c 'arr=(); while true; do arr+=("$(head -c 10485760 </dev/zero | tr "\0" "\1")"); sleep 0.2; done'
```

If all is configured well, you should see the memory increase up to the limit we have set in Dokploy, and the container should be restarted automatically.

**If you have not properly set a memory limit in Dokploy, the script will keep consuming memory untill the entire VPS crashes.** So, please monitor and press ctrl+c to stop the script if you see the memory usage is exceeding your container limit.

## CPU Isolation

We are going to make the Vendure Worker do a CPU intensive task, and check if it does not exceed the limit we have set in Dokploy.
1. SSH in to your VPS and run `docker ps` to find the container ID of the Vendure Worker.
2. Run `docker exec -it <container_name_or_id> sh -c "yes > /dev/null & yes > /dev/null & yes > /dev/null"` to make the Worker consume CPU. You can press `Ctrl+C` to stop the process.
3. Go to Application > Vendure Worker > Monitoring
4. If you have set the CPU limit to `500000000`, then the CPU usage should stay at around 50%.

:warning: Keep in mind that most monitoring tools will show CPU usage per core! So seeing 150% CPU usage means 1.5 cores are being used.

