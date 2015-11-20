# Nagios Webchecks Generator #

Using Nagios is great, writing lots of mostly boilerplate tests isn't.
The Nagios Webchecks Generator was written in order to remove some of
the tedium.

The Nagios Webchecks Generator consists of three parts:

 * webchecks.ini - contains the IPs you want to check and the Nagios commands used to check them.
 * a template that expands to the checks you defined against each specified host
 * generate-webchecks - reads the config and template and produces the final checks.

In the config files (called `webchecks.ini` by default) there are two
types of configs. First are named groups. Each one consists of a group
name and a comma separated list of IP addresses:

    staticserver = 10.10.10.1, 10.10.10.2, 10.10.10.3

You then define the checks that you want to run against each IP in the
group.

    [check::Dynamics About]
    group   = dynamics
    command = /usr/lib/nagios/plugins/check_http -w 6 -c 10 -H dynamics.int.example.org -I %s -u /about -s "About us"

You should note the `%s` in the command line. When the template is
interpolated this placeholder is replaced with the respective IP address.

You then run `./generate-webchecks -c webchecks.ini -t nagios-webchecks.tt` to
produce the nagios service definitions.

## Usage notes ##

Each site we run has at least one server in the staging environment, a
number of live servers and a load balanced address. By defining all those
IPs in a named group you can consistently deploy checks across all your
environments. Once you have these checks in place they provide an extra
safety blanket, if a change breaks the staging check then you can fix it
in staging, redeploy the nagios checks, watch live go red, do the
release and watch the red go green.

## Notes ##

The shell template was written in order to facilitate testing and to
provide a safety net. Once you've run the generate command you can
immediately run all the tests and receive instant feedback.

    # generate the shell commands
    ./generate-webchecks -c webchecks.ini -t shell-webchecks.tt > out
    # run them for immediate feedback.
    bash -x out

### License ###

This code is released under the GPL v2 - [Dean Wilson](http://www.unixdaemon.net)
