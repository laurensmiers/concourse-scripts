Concourse scripts
============

This is a collection of concourse CI scripts/pipelines/resources/... I use in my personal CI.
Mostly as reference for me if I ever have to setup something.

For now, I didn't setup any credentials manager yet, so all secrets (passwords, usernames, ...) are in separate yaml files (secrets folder).
Once I have setup Vault (or something like that), I can get rid of this folder.

Login to server
------------

~~~ bash
$ fly -t main login -c http://server.lan:8081
~~~

Visit http://server.lan:8081 for the Concourse CI dashboard, download the 'fly' cli tool.

Suckless Terminal (ST)
------------

To setup the st pipelines, we have tree steps:
  * Building the st-build docker image
  * Building st application
  * Publishing the new build if the build step succeeds

### Building the ST build docker image header ###

Setup the pipeline with:

~~~ bash
$ fly -t main set-pipeline -p st-docker -c ./pipelines/st/st-image.yml -l ./secrets/docker-hub.yml
~~~

Unpause it (through CLI, can also do this via web browser):
~~~ bash
$ fly -t main unpause-pipeline -p st-docker
~~~

Start the build (through CLI, again possible to do this via web browser):
~~~ bash
$ fly -t main trigger-job -j st-docker/publish
~~~

### Building the ST application ###

Setup the pipeline with:

~~~ bash
$ fly -t main set-pipeline -p st-build -c ./pipelines/st/st-pipeline.yml -l ./secrets/docker-hub.yml
~~~

Unpause it (through CLI, can also do this via web browser):
~~~ bash
$ fly -t main unpause-pipeline -p st-build
~~~

Start the build (through CLI, again possible to do this via web browser):
~~~ bash
$ fly -t main trigger-job -j st-build/st-job
~~~

### Publishing new build/release ###

This is still a TODO. I haven't found a decent way yet to publish releases/builds to a release page (don't have that either yet...).


TODO
------------

* Release page
* Push releases to a release page
* Dependency between pipelines
  f.e. st-docker should trigger a new build of st-build
