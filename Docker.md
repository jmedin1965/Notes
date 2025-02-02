# Docker
Created Thursday 30 January 2025

Home Assistant firefox-java-vnc-flash
-------------------------------------

This is my first, and an example on how to build a docker image. I had this working for along time on docker and am converting it into a Home Assistant addon. I use it to get a remote desktop session to a very old CISCO server that does not have HTML5, so it still uses Java and Flash.


1. Build a base image, with basic run.sh command
2. Upload image to github Packages Docker repo
	1. REF: <https://www.youtube.com/watch?v=RgZyX-e6W9E>
	2. Generate a tocken
	3. docker login --username [jmedin1965](./jmedin1965.md) --password the-tokenghcro.io
	4. docker build . -t ghcr.io/jmedin1965/firefox-java-vnc-flash-base:1.1.1 -t ghcr.io/jmedin1965/firefox-java-vnc-flash-base:latest
	5. docker push ghcr.io/jmedin1965/firefox-java-vnc-flash-base:latest
	6. This example also shows how to use github actions to auto generate the image every time a git push is done on a repo


Techno Tim : Build YOUR OWN Dockerfile, Image, and Container - Docker Tutorial
------------------------------------------------------------------------------

<https://www.youtube.com/watch?v=SnSH8Ht3MIc>


Watchtower (Jason recomendation)
--------------------------------

With watchtower you can update the running version of your containerized app simply by pushing a new image to the Docker Hub or your own image registry.

Watchtower will pull down your new image, gracefully shut down your existing container and restart it with the same options that were used when it was deployed initially. Run the watchtower container with the following command:


