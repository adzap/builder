# Deis Builder v2

[![Build Status](https://travis-ci.org/deis/builder.svg?branch=master)](https://travis-ci.org/deis/builder) [![Go Report Card](http://goreportcard.com/badge/deis/builder)](http://goreportcard.com/report/deis/builder) [![Docker Repository on Quay](https://quay.io/repository/deisci/builder/status "Docker Repository on Quay")](https://quay.io/repository/deisci/builder)

Deis (pronounced DAY-iss) is an open source PaaS that makes it easy to deploy and manage applications on your own servers. Deis builds on [Kubernetes](http://kubernetes.io/) to provide a lightweight, easy and secure way to deploy your code to production.

## Beta Status

This Deis component is currently in beta status, and we welcome your input! If you have feedback, please [submit an issue](https://github.com/deis/builder/issues). If you'd like to participate in development, please read the "Development" section below and [submit a pull request](https://github.com/deis/builder/pulls).

# About

The builder is primarily a git server that responds to `git push`es from clients. When it receives a push, it takes the below high level steps in order.

1. Accepts the code and writes it to the local file system
2. Calls `git archive` to produce a tarball (i.e. a `.tar.gz` file) on the local file system
3. Saves the tarball to centralized object storage according to the following rules:
  - If the `DEIS_MINIO_SERVICE_HOST` and `DEIS_MINIO_SERVICE_PORT` environment variables exist, saves to the [S3 API](http://docs.aws.amazon.com/AmazonS3/latest/API/APIRest.html) compatible server at `http://$DEIS_MINIO_SERVICE_HOST:$DEIS_MINIO_SERVICE_HOST`
  - Otherwise, if the `DEIS_OUTSIDE_STORAGE` environment variable exists, saves to `https://$DEIS_OUTSIDE_STORAGE`.
4. Starts a new [Kubernetes Pod](http://kubernetes.io/docs/user-guide/pods/) to build the code, according to the following rules:
  - If a `Dockerfile` is present in the codebase, starts a [`dockerbuilder`](https://github.com/deis/dockerbuilder) pod, configured to download the code to build from the URL computed in the previous step.
  - Otherwise, starts a [`slugbuilder`](https://github.com/deis/slugbuilder) pod, configured to download the code to build from the URL computed in the previous step.

# Development

As mentioned above, we welcome contributions from all developers. The high level process for development matches many other open source projects. See below for an outline.

* Fork this repository
* Make your changes
* [Submit a pull request](https://github.com/deis/builder/pulls) (PR) to this repository with your changes, and unit tests whenever possible.
	* If your PR fixes any [issues](https://github.com/deis/builder/issues), make sure you write `Fixes #1234` in your PR description (where `#1234` is the number of the issue you're closing)
* The Deis core contributors will review your code. After each of them sign off on your code, they'll label your PR with `LGTM1` and `LGTM2` (respectively). Once that happens, you may merge.

## Docker Based Development Environment

Our preferred method of day-to-day development is using [our `go-dev` Docker image](https://github.com/deis/docker-go-dev). We use the tools described in this section to build, test, package and release each version of Deis.

To use it yourself, we require that you have [make](https://www.gnu.org/software/make/) installed and Docker installed and running on your local development machine to use this method of development.

If you don't have Docker installed, please go to https://www.docker.com/ to install it.

After you have those dependencies, you can build and unit-test your code with `make build` and `make test`, respectively.

## Native Go Development Environment

Although we prefer the Docker based development environment, you can also use the standard `go` toolchain to build and test. To do so, you'll [glide](https://github.com/Masterminds/glide) 0.9 or above and [Go 1.6](http://golang.org) or above.

After you have those dependencies, you can build and unit-test your code with `go build` and `go test $(glide nv)`, respectively.

Note that you will not be able to build or push Docker images using this method of development.

# Testing

We require that as much code as possible is unit tested, but also recognize that some code must be tested at a higher level (functional or integration tests, for example). In order to set up and run such tests, we've built [end-to-end tests](https://github.com/deis/workflow-e2e) and also periodically dogfood our product. Since this component is at the center of much of the Deis platform, we find it especially important to dogfood it.

## Running End to End Tests

Please see [README.md](https://github.com/deis/workflow-e2e/blob/master/README.md) on the end to end tests reposotory for instructions on how to set up your testing environment and run the tests.

## Dogfooding

Please follow the instructions on the [official Deis docs](http://docs-v2.readthedocs.org/en/latest/installing-workflow/installing-deis-workflow/) to install and configure your Deis cluster and all related tools, and deploy and configure an app on Deis.

# License

Copyright 2013, 2014, 2015, 2016 Engine Yard, Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at <http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.


[install-k8s]: http://kubernetes.io/gettingstarted/
