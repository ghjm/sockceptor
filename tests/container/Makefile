IMAGE_TAG_BASE=ansible/receptor:base
IMAGE_TAG_TEST=ansible/receptor:test

image: image-base

image-base:
	podman build -t $(IMAGE_TAG_BASE) -f ./base/Dockerfile ../..

# image-test:
# 	podman build -t $(IMAGE_TAG_BASE) -f ./test/Dockerfile ../..

shell-base:
	podman run -it --rm \
		-v $(PWD)/../../:/source/:ro \
		$(IMAGE_TAG_BASE) bash

# shell-test:
# 	podman run -it --rm \
# 		-v $(PWD)/../../:/source/:ro \
# 		$(IMAGE_TAG_BASE) bash

artifacts: image-base
	mkdir -p $(PWD)/artifacts
	podman run -it --rm \
		-v $(PWD)/../../:/source/:ro \
		-v $(PWD)/artifacts/:/artifacts/:rw \
		$(IMAGE_TAG_BASE) \
		/build-artifacts.sh
