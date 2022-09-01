#!/usr/bin/make
SHELL=/bin/bash

NAMESPACE = cacotopia
NAME = oracle-jdk-maven
FULLNAME = $(NAMESPACE)/$(NAME)
TAG = jdk-8u202-mvn-3.8.1
LATEST_TAG = latest

.DEFAULT_GOAL := help
.PHONY: help
# .PHONY: all clean build tag_latest release debug run save start usage

all: usage build

clean:
	@CID=$(docker ps -a | awk '{ print $$1 " " $$2 }' | grep $(FULLNAME) | awk '{ print $$1 }'); if [ ! -z "$$CID" ]; then echo "Removing container which reference $(FULLNAME)"; for container in $(CID); do docker rm -f $$container; done; fi;
	@echo "Removing image $(FULLNAME)"
	@if docker images $(FULLNAME) | awk '{ print $$2 }' | grep -q -F $(VERSION); then docker rmi -f $(FULLNAME):$(VERSION); fi
	@if docker images $(FULLNAME) | awk '{ print $$2 }' | grep -q -F latest; then docker rmi -f $(FULLNAME):latest; fi

## Docker image build
build: clean
	docker build -t $(FULLNAME):$(TAG) -f Dockerfile --rm .

 ## Docker image build
build-latest:
	docker build -t ${FULLNAME}:$(LATEST_TAG) -f Dockerfile ./

tag_latest:
	docker tag $(FULLNAME):$(TAG) $(FULLNAME):$(LATEST_TAG)

## Docker image build multi-platform
buildx: 
	docker buildx build \
	    --push \
        --platform=linux/amd64,linux/arm64 \
        --tag=${FULLNAME}:${TAG} \
        -f Dockerfile ./

## Docker image build multi-platform
buildx-latest: 
	docker buildx build \
	    --push \
        --platform=linux/amd64,linux/arm64 \
        --tag=${FULLNAME}:$(LATEST_TAG) \
        -f Dockerfile ./

## Docker image push
release: build tag_latest
	docker push $(FULLNAME):${TAG}
	@echo "Create a tag v-$(TAG)"
	@git tag v-$(TAG)
	@git push origin v-$(TAG)

release-latest: ## Docker image push
	docker push ${FULLNAME}:latest

debug:
	@docker run -t -i $(FULLNAME):$(TAG) /bin/bash

save:
	@OUTPUT_FILE =  $(NAME)_$(TAG)_`date +%Y%m%d%H%M%S`.tgz
	@docker save $(FULLNAME):$(TAG) | gzip --best --stdout  > $(OUTPUT_FILE)
	@echo "Image $(FULLNAME):$(TAG) exported to $(OUTPUT_FILE)"

run:
	@echo "IPAddress =" $$(docker inspect --format '{{.NetworkSettings.IPAddress}}' $$(docker run -d $(FULLNAME):$(TAG)))

start:
	@docker-machine start default
	@eval "$(docker-machine env default)"

help: ## Show this help
	@printf "\033[33m%s:\033[0m\n" 'Available commands'
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "  \033[32m%-14s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)

usage:
	@echo "List of target"
	@echo "all (default): print usage and build"
	@echo "clean:         remove containers and image"
	@echo "build:         build docker image"
	@echo "tag_latest:    build and tag image with 'latest'"
	@echo "debug:         launch a shell with this image"
	@echo "save:          export this image"
	@echo "run:           launch this image with inner command"
	@echo "start:         start docker machine"
	@echo "usage:         this help"
