TESTS = $(shell ls -S `find test -type f -name "*.test.js" -print`)
TIMEOUT = 10000
MOCHA_OPTS =
REPORTER = tap
NPM_REGISTRY = --registry=http://registry.npm.taobao.net
NPM_INSTALL_PRODUCTION = PYTHON=`which python2.6` NODE_ENV=production npm install $(NPM_REGISTRY)
NPM_INSTALL_TEST = PYTHON=`which python2.6` NODE_ENV=test npm install $(NPM_REGISTRY)

install:
	@$(NPM_INSTALL_PRODUCTION)

install-test:
	@$(NPM_INSTALL_TEST)

test: install-test
	@NODE_ENV=test ./node_modules/.bin/mocha \
		--reporter $(REPORTER) --timeout $(TIMEOUT) $(MOCHA_OPTS) $(TESTS)

test-cov-html:
	@rm -f coverage.html
	@$(MAKE) test MOCHA_OPTS='--require blanket' REPORTER=html-cov > coverage.html
	@$(MAKE) test MOCHA_OPTS='--require blanket' REPORTER=travis-cov
	@ls -lh coverage.html

test-cov: test-cov-html
	@$(MAKE) test MOCHA_OPTS='--require blanket' REPORTER=travis-cov

test-all: test test-cov

.PHONY: test
