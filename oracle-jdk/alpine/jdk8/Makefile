#!/usr/bin/make
SHELL=/bin/bash
IMAGE = cacotopia/oracle-jdk8-alpine
TAG = 8u202

.DEFAULT_GOAL := help
.PHONY: help

help: ## Show this help
	@printf "\033[33m%s:\033[0m\n" 'Available commands'
	@awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z_-]+:.*?## / {printf "  \033[32m%-14s\033[0m %s\n", $$1, $$2}' $(MAKEFILE_LIST)

build: ## Docker image build
	docker build -t ${IMAGE}:${TAG} -f Dockerfile ./

build-latest: ## Docker image build
	docker build -t ${IMAGE}:latest -f Dockerfile ./

buildx: ## Docker image build multi-platform
	docker buildx build \
	    --push \
        --platform=linux/amd64,linux/arm64 \
        --tag=${IMAGE}:${TAG} \
        -f Dockerfile ./

buildx-latest: ## Docker image build multi-platform
	docker buildx build \
	    --push \
        --platform=linux/amd64,linux/arm64 \
        --tag=${IMAGE}:latest \
        -f Dockerfile ./

push-latest: ## Docker image push
	docker push ${IMAGE}:latest

push: ## Docker image push
	docker push ${IMAGE}:${TAG}
