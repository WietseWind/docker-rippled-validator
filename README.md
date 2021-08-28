# Rippled Validator

This container allows you to run a `rippled` validator. No config required; however: make sure port `51235` is opened up. If you are behind a firewall: open the port. If you are behind a NAT router: set up port forwarding.

More info about validators? Check the Ripple website:
[https://ripple.com/build/rippled-setup/#reasons-to-run-a-validator](https://ripple.com/build/rippled-setup/#reasons-to-run-a-validator)

This container is running on `ubuntu:latest`.

## How to run

If you are new to this and you want to deploy a server for $20 a month at DigitalOcean: [here's a howto](https://medium.com/@WietseWind/how-to-run-a-ripple-validator-digitalocean-7e5fca1c3d77).

### From Github

If you downloaded / cloned the [Github repo](https://github.com/WietseWind/docker-rippled-validator) you got yourself a few scripts to get started. In the `./go` folder, the following scripts are available, run:

- `go/build` to build the container image (tag: `rippledvalidator:latest`)
- `go/up` to create a new container named `rippledvalidator` and setup the port and persistent config (*1)
- `go/down` to stop and remove the container `rippledvalidator`

The `go/up` command will mount the subfolder (in the cloned repo folder) `keystore` to the container; the rippled config, validators config and validator keypair will be saved in this folder. If you stop/start or restart the container, the container will pickup your existing config and keypairs :)

If you want to build the image manually, use (you can change the tag):

```
docker build --tag rippledvalidator:latest .
```

### From the Docker Hub

Use the image `xrptipbot/rippledvalidator`.

**Because you only retrieved the container image from the Docker Hub, you have to manually create a container based on the image.** When creating the container, please make sure you open port `51235`.

When the container is launched for the first time it will generate the required configfiles for rippled and create a validator keypair. If you're not just testing, you want to save these files. This way, next time you fire up the container (eg. after a restart) you are still "the same" validator. To do this, you have to specify a volume mounted to `/keystore` (inside the container).

This command does the trick:

```
docker run -dit \
    --name rippledvalidator \
    -p 51235:51235 \
    -v /My/Local/Disk/RippledKeystore/:/keystore/ \
    xrptipbot/rippledvalidator:latest
```

You can change the `--name` and **make sure you specify a valid local full path for your volume source, instead of `/My/Local/Disk/RippledKeystore/`**.

This folder wil contain the generated `rippled.cfg`, `validator-keys.json` and `validators.txt`.

## So it's running

If everything is OK and your server is accessible from the public internet, you should be able to find your server (after ~15 minutes) in the Validator Registry: [https://xrpcharts.ripple.com/#/validators](https://xrpcharts.ripple.com/#/validators).

You can retrieve your own _Validator Public Key_ by checking the `validator-keys.json` file in the folder containing the config (`./keystore/` or the path you specified for your volume mapping), or by running:

```
docker exec rippledvalidator cat /root/.ripple/validator-keys.json|grep public_key
```

If you want to check the rippled-logs (container stdout, press CTRL - C to stop watching):

```
docker logs -f rippledvalidator
```

If you want to check the rippled server status:

```
docker exec rippledvalidator server_info
```

If you started the container manually, you may have to change the name of the container (`rippledvalidator`) to the name you entered in your `docker run` command.

## If you want to keep your validator running

... You probably want to validate your domain (Domain Verification: [https://ripple.com/build/rippled-setup/#domain-verification](https://ripple.com/build/rippled-setup/#domain-verification). Since the `rippled` and `validator-keys` binaries are not on your local computer, you'll have to run them inside the docker container. All you have to do is prepend the `docker exec` command with your container name before the commands Ripple is showing;

So:

```
/opt/ripple/bin/rippled server_info -q | grep pubkey_validator
```

... becomes:

```
docker exec rippledvalidator /opt/ripple/bin/rippled server_info -q | grep pubkey_validator
```

If you started the container manually, you may have to change the name of the container (`rippledvalidator`) to the name you entered in your `docker run` command.

When you need to provide the contents of your domain private key, you can add your domain private key to the `keystore` folder (or volume mount you made), the contents are mapped to `/keystore/` in the container.

# Updating

- **2018-02-21** rippled 0.90.0 [is released](https://ripple.com/dev-blog/rippled-version-0-90-0/)
- **2018-03-23** rippled 0.90.1 [is released](https://ripple.com/dev-blog/rippled-version-0-90-1/)
- **2018-05-15** rippled 1.0.0 [is released](https://twitter.com/nbougalis/status/996385729646297090)
- **2018-06-14** rippled 1.0.1 [is released](https://ripple.com/dev-blog/rippled-version-1-0-1/)
- **2018-09-15** rippled 1.1.0 [is released](https://github.com/ripple/rippled/releases/tag/1.1.0)
- **2018-10-23** rippled 1.1.1 [is released](https://ripple.com/dev-blog/rippled-version-1-1-1/)
- **2018-12-12** rippled 1.1.2 [is released](https://ripple.com/dev-blog/introducing-xrp-ledger-rippled-version-1-1-2/)
- **2019-02-14** rippled 1.2.0 [is released](https://github.com/ripple/rippled/releases/tag/1.2.0)
- **2019-02-27** rippled 1.2.1 [is released](https://github.com/ripple/rippled/releases/tag/1.2.1)
- **2019-03-07** rippled 1.2.2 [is released](https://github.com/ripple/rippled/releases/tag/1.2.2)
- **2019-04-03** rippled 1.2.3 [is released](https://github.com/ripple/rippled/releases/tag/1.2.3)
- **2019-04-17** rippled 1.2.4 [is released](https://github.com/ripple/rippled/releases/tag/1.2.4) 
- **2019-07-26** rippled 1.3.1 [is released](https://xrpl.org/blog/2019/rippled-1.3.1.html) 
- **2019-07-26** rippled 1.4.0 [is released](https://xrpl.org/blog/2019/rippled-1.4.0.html)
- **2020-04-01** rippled 1.5.0 [is released](https://xrpl.org/blog/2020/rippled-1.5.0.html)
- **2020-08-19** rippled 1.6.0 [is released](https://xrpl.org/blog/2020/rippled-1.6.0.html)
- **2021-02-24** rippled 1.7.0 [is released](https://xrpl.org/blog/2021/rippled-1.7.0.html)
- **2021-05-24** rippled 1.7.2 is released
- **2021-08-28** rippled 1.7.3 is released

## Update process

1. Stop the container: `docker stop rippledvalidator` (if you named (`--name`) the container `rippled`)
2. Remove the container: `docker rm rippledvalidator`
3. Remove the image: `docker rmi xrptipbot/rippledvalidator:latest` (or if you built the container image based on the [Github repo](https://github.com/WietseWind/docker-rippled-validator): use the image name you specified when building)
4. Re-create the container; if you used Git: `git pull` and `go/build` - if you used the Docker Hub: just use the command from this Readme (_From the Docker Hub_), a new version of the image will be downloaded.

**USE THE PATHS YOU SPECIFIED (`-v` argument) WHEN RECREATING THE CONTAINER IF YOU WANT TO KEEP YOUR CONFIG AND/OR DATA!**
