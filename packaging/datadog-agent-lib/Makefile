
.PHONY: clean links dirs deb rpm install

BUILD=build/package
CWD=$(shell pwd)

FPM_BUILD=fpm -s dir -C $(BUILD) -e -n "datadog-agent-lib" \
	 -a all -m "Datadog Packages <package@datadoghq.com>" \
     --url "http://www.datadoghq.com/" \
	 --description "`cat desc`"\
     -v `python -c "from setup import printVersion; printVersion()"` \

clean:
	rm -f checks
	rm -rf build*
	rm -rf *.egg-info
	rm -rf dist
	rm -rf *.deb
	rm -rf *.rpm
	rm -rf *.pyc

links:
	ln -sf ../../checks .

dirs:
	mkdir -p $(BUILD)

install: dirs links
	PYTHONPATH=$(BUILD) python setup.py install --root=$(BUILD) --install-layout=deb --install-lib=/usr/share/datadog/agent --install-scripts=/usr/share/datadog/agent --no-compile
	rm -rf $(BUILD)/home
	rm -rf $(BUILD)/usr/share/datadog/agent/*.egg-info

deb: clean install
	FPM_EDITOR="echo 'Replaces: datadog-agent (<< 2.0), datadog-agent-base (<< 2.1.1)' >>" \
		$(FPM_BUILD) -t deb \
		-d "python (>= 2.4)" \
        .

rpm: clean install
	FPM_EDITOR="$(CWD)/rpm/inject.sh" \
    	$(FPM_BUILD) -t rpm \
		-d "python" \
        .
