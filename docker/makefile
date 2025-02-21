.PHONY: image container
image: Dockerfile
	docker build -f Dockerfile -t orb_slam3 ..

container: image
	docker run --device /dev/video0 --device=/dev/video1 --device=/dev/media0 -v /run/udev:/run/udev:ro -it --rm --name orb_slam3 orb_slam3