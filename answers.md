Patrick McBrien

DataDog Answer
08/07/17

**I will expand on what others have already accomplished**

Taking a step back i realized that anything on the internet or premise can be monitored with the agent. I got the agent running flawlessly in minutes which was really nice.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/metrics.png)

**What can the product and agent do?**

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/linking-to_aws.png)

I can look at many graphs and metrics such as CPU usage over a specified time period, but knowing this let's go further. I have data is coming from a lcaol Vagrant VM but would love to setup AWS and get cloud monitoring setup. An external key is provided and the setup was a breeze. So far I have several datadog agents running with the metrics coming in from these 3 sources. 

1. AWS including ec2 instances and disks. Just type in the API key, create a IAM role and you are AWS cloud monitoring.
2. Datadog agent on Vagrant Ubuntu based VM
3. Locally on a Macintosh running under OSX

It really does not matter where the agent gets installed! You can even install it on any IOT device, or even Rasberry PI or Arduino, or a nuclear reactor or a dam. You could even alert on something like temperature or humidity or any 3rd party or custom application. However, under normal circumstances a customer would likely monitor a web server, a web application, a cluster, or perhaps hard drive space. 

The agent basically communicates any data you would like back to be visualized on graphs, screenshot and emailed, or databased for future reporting and archiving purposes. There are many example yaml files to choose from.

I went ahead with the agent and installed in both locations using the same account access key unser same account on datadoghq.com. I also went ahead and installed a LAMP stack as well as mysql server under a very basic Vagrant image. This is a very easy to use system and I really do like using it. I have also uploaded some of the tools that helped along the way to github. Docker is another solution that would work.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/downloadvagrant.png)

**Autoinstall the Datadog Agent, Spin up Vagrant using Ubuntu based LAMP stack, in one easy shell script**

	#!/bin/bash
	mkdir datadog
	cd datadog

	vagrant init hashicorp/precise64
	vagrant up

	vagrant ssh -c 'sudo apt-get -y update'
	vagrant ssh -c 'sudo apt-get -y install curl apache2'
	vagrant ssh -c 'sudo apt-get -y install mysql-server mongodb'
	vagrant ssh -c 'sudo apt-get -y install php5 libapache2-mod-php5 php5-mcrypt'
	vagrant ssh -c 'DD_API_KEY=API_KEY_GOES_HERE bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/dd-   agent/master/packaging/datadog-agent/source/install_agent.sh)"'

	vagrant ssh

	#This command worked on Ubuntu to get the agent installed by itself

	#DD_API_KEY=X123123XXXXXYOURKEY bash -c "$(curl -L https://raw.githubusercontent.com/DataDog/dd-agent/master/packaging/datadog-agent/source/install_agent.sh)"

**Integrate something to monitor, I will be including a webserver and a database server**

I headed on over to datadoghq.com and created an account that took care of the explaining of most of the integrations. The icons are arranged nicely and mention every devops tool i have ever heard of and more. They even have a step by step of commands needed to integrate. 

Whether it is bare metal, virtual machines or even communicating to my servers in the cloud is pretty easy to do with no knowledge needed depending on cups of coffee consumed. Datadog is making it easier for people to monitor infrastructure and web applications, that is for sure.  Vagrant is up and running using a precise64 base is the image we went with. The agent has infinite possibilities beyond normal metrics like CPU and disk or memory utilization.  The agent is like your favorite dog playing fetch. I can also write my own custom itegrations, or use ones that datadog has written just like the MySQL exampl provided. I am going to be using a Vagrant VM, install virtualbox locally and get the datadog agent installed to monitor mysqld. So far everything has gone smoothly.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/installing_virtualbox_mac.png)

In the meantime, We are showing multiple hosts in the infrastructure area of datadoghq.com! First is my precise64 Ubuntu box, then my Macintosh, then we have a few Amazon cloud ec2 instances.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/mysql.png)

Now we can see that our all of our service(s) are being monitored including MYSQL, HTTP using Apache, NTP. We can now start a monitor or add a new service integtration with a few steps that can be found in datadog documentaion.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/ServicesUpRunning.png)

Here is an example of an AWS alert with team notifications

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/AWSalertNotify.png)

**Here is my infrastructure running myrandomcheck and a test program that collects data**

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/CustomIntegration.png)

Setting up a basic monitor with tags using widgets centered around time periods

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/Monitor.png)

You can also see here that the metrics area now has our custom random check. Check out /var/log/datadog to see where the bugs in the yaml or py scripts are. When a service is highlights yellow, you know that there could be a bug in the code.

https://app.datadoghq.com/dash/336506?live=true&page=0&is_auto=false&from_ts=1502153472687&to_ts=1502157072687&tile_size=m

Bonus: Difference between Timeboard and Screenboard.

A timeboard is an extremely robust tool for viewing top level metrics of a system for correlation of data and troubleshooting.

Screenboards are for sharing data with others

Take a look at my graph going over .9

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/Overload.png)

Time Series AVG

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/TimeSeries.png)

The monitor is also installed now with a threshold of .9. Not only can you select time periods like 5m 1h etc. you can overlay different datapoints and use any mathematics you like.

**Alerting on data**

Where its deviation or a jumped number, switches can be pushed. Alerts can fire, any chain of events can be strung together.

	Threshold Alerts
	Change Alerts
	Anomoly
	Outlier

https://app.datadoghq.com/monitors#2600683?group=all&live=4h

**Email has arrived**

You know you are close to the finish line when you get an email like the one below. It means the .9 threshold from our custom integration has been triggered.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/emailddog.png)

While you are sleeping, turn off the alerts. You can choose any time you think would be acceptable to mute the emails.

![ScreenShot](https://raw.github.com/pmcbrien/hiring-engineers/master/ddog/Mute.png)

**HTTP CHECK**

There are a ton of scripts in the folder so I wrote a nice tool that checks on my website and looks for a 200. A POST or a GET can be done if you like.

	init_config:
	  # Change default path of trusted certificates
	  # ca_certs: /etc/ssl/certs/ca-certificates.crt

	instances:
	  - name: My first service
	    url: http://www.dynowax.com
	    timeout: 1

	    # The default HTTP method is GET but the (optional) method parameter allows you to change the
	    # HTTP method to POST.
	    # If using the POST method, you can then specify the data to send in the body of the request
	    # with the data parameter.
	    # SOAP requests are supported if you use the POST method and supply an XML string as the data parameter.
	    #
	    # method: get
	    # data:
	    #   key: value

	    # The (optional) content_match parameter will allow the check
	    # to look for a particular string within the response. The check
	    # will report as DOWN if the string is not found.
	    #
	    # content_match uses Python regular expressions which means that
	    # you will have to escape the following "special" characters with
	    # a backslash (\) if you're trying to match them in your content:
	    #  . ^ $ * + ? { } [ ] \ | ( )
	    #
	    # Examples:
	    content_match: 'Wax'
	    # content_match: '^(Bread|Apples|Very small rocks|Cider|Gravy|Cherries|Mud|Churches|Lead) float(s)? in water'

	    # The (optional) reverse_content_match parameter will allow the content_match
	    # to work the other way around. That means that the check will report
	    # as DOWN if the string is found.
	    # reverse_content_match: false

	    # If your service uses basic authentication, you can optionally
	    # specify a username and password that will be used in the check.
	    #
	    # username: user
	    # password: pass

	    # The (optional) http_response_status_code parameter will instruct the check
	    # to look for a particular HTTP response status code or a Regex identifying
	    # a set of possible status codes.
	    # The check will report as DOWN if status code returned differs.
	    # This defaults to 1xx, 2xx and 3xx HTTP status code: (1|2|3)\d\d.
	    # http_response_status_code: 401

	    # The (optional) include_content parameter will instruct the check
	    # to include the first 200 characters of the HTTP response body
	    # in notifications sent by this plugin. This is best used with
	    # "healthcheck"-type URLs, where the body contains a brief, human-
	    # readable summary of failure reasons in the case of errors. This
	    # defaults to false.
	    #
	    # include_content: false

	    # The (optional) collect_response_time parameter will instruct the
	    # check to create a metric 'network.http.response_time', tagged with
	    # the url, reporting the response time in seconds.
	    #
	    collect_response_time: true

	    # The (optional) disable_ssl_validation will instruct the check
	    # to skip the validation of the SSL certificate of the URL being tested.
	    # This is mostly useful when checking SSL connections signed with
	    # certificates that are not themselves signed by a public authority.
	    # When true, the check logs a warning in collector.log
	    # Defaults to true, set to false if you want SSL certificate validation.
	    #
	    disable_ssl_validation: true

	    # If you enable the disable_ssl_validation above, you might want to disable security warnings
	    # You can use the flag below to do so
	    # ignore_ssl_warning: false

	    # Path of trusted CA certificates used to validate the SSL certificate
	    # for this url.
	    # Overrides the default path and the one specified in init_config.
	    #
	    # ca_certs: /etc/ssl/certs/ca-certificates.crt

	    # The (optional) check_certificate_expiration will instruct the check
	    # to create a service check that checks the expiration of the
	    # ssl certificate. Allow for a warning to occur when x days are
	    # left in the certificate, and alternatively raise a critical
	    # warning if the certificate is y days from the expiration date.
	    # The SSL certificate will always be validated for this additional
	    # service check regardless of the value of disable_ssl_validation
	    #
	    # check_certificate_expiration: true
	    # days_warning: 14
	    # days_critical: 7

	    # The (optional) headers parameter allows you to send extra headers
	    # with the request. This is useful for explicitly specifying the host
	    # header or perhaps adding headers for authorisation purposes. Note
	    # that the http client library converts all headers to lowercase.
	    # This is legal according to RFC2616
	    # (See: http://tools.ietf.org/html/rfc2616#section-4.2)
	    # but may be problematic with some HTTP servers
	    # (See: https://code.google.com/p/httplib2/issues/detail?id=169)
	    #
	    #headers:
	    #3   Host: alternative.host.example.com
	    #X-Auth-Token: DataDog-AUTH-TOKEN

	    # The (optional) skip_event parameter will instruct the check to not
	    # create any event to avoid duplicates with a server side service check.
	    # This default to False.
	    #
	    skip_event: true

	    # The (optional) no_proxy parameter would bypass any proxy settings enabled
	    # and attempt to reach the the URL directly.
	    # If no proxy is defined at any level, this flag bears no effect.
	    # Defaults to False.
	    #
	    # no_proxy: false

	    # The (optional) allow_redirects parameter can enable redirection.
	    # Defaults to True.
	    #
	    # allow_redirects: true


	    # The (optional) include_default_header parameter instructs the check to include the default headers.
	    # Default headers can be found at https://github.com/DataDog/dd-agent/blob/5.10.0/util.py#L58-L67
	    #
	    # Defaults to True.
	    #
	    #include_default_headers: false

	    # tags:
	    #   - url:http://alternative.host.example.com
	    #   - env:production

	  # - name: My second service
	  #   url: https://another.url.example.com
	 	    
**MYSQL CUSTOM METRIC CHECK**

In the mysql.yaml file you can run a query to check a certain field or row in the database. This is a great way to find data issues or other problems in your database content, structure, or cluster/network. You can ensure the database is working by inserting a row every few minutes and comparing timestamps to those previous entries.

After some debugging, I was able to get http, mysql, and a custom check working properly

I would love to take this to the next level.

Patrick McBrien

