---
layout: post
title:  "Simple Event Correlator: A Practical Guide"
date:   2012-02-23 12:00:00
categories: linux
---

![article word cloud](/assets/sec.png)

<a href="http://simple-evcorr.sourceforge.net/">Simple Event Correlator</a> (SEC) can be configured to ignore routine errors and alert you to serious errors or errors that have never occurred before. In this short article, I will describe how to integrate SEC with syslog-ng. I will describe the following:
<ul>
	<li>Installing SEC</li>
	<li>Configuring SEC</li>
	<li>Testing the SEC Configuration</li>
	<li>Configuring syslog-ng</li>
	<li>Next Steps</li>
</ul>
<h2>Installing SEC</h2>
<a href="http://simple-evcorr.sourceforge.net/" target="_blank">Download</a> SEC. Copy <code>sec.pl</code> to <code>/usr/local/bin</code> and mark it executable.
<h2>Configuring SEC</h2>
Create the SEC configuration file, <code>/usr/local/etc/sec.conf</code>, marked as world-readable. Edit the file as appropriate (you’ll need to understand regular expressions and read the SEC documentation). Here is an <em>example</em>:
<pre>###
# /usr/local/etc/sec.conf
#
##
# Suppress Workstations
#
type=Suppress
ptype=regexp
pattern=S+s+S+s+S+s+(tpr|dpr|bpr|pre)
##
# Combine unknown errors by daemon each hour and report
#
type=Single
ptype=regexp
pattern=S+s+S+s+S+s+(S+)s+(w+).*:s+(.*)
desc=$1 $2
context=!$1_$2
action=create $1_$2 3600 (report $1_$2 /bin/mail -s
"LogAlert: $2 errors summary" helpdesk@example.com);
pipe '$0' /bin/mail -s "LogAlert: $2 error, suppressing similar for 1 hour" helpdesk@example.com

type=Single
ptype=regexp
pattern=S+s+S+s+S+s+(S+)s+(w+).*:s+(.*)
desc=$1 $2
context=$1_$2
action=add $1_$2 $0</pre>
<h2>Testing the SEC Configuration</h2>
SEC provides a test-only mode. Run the following, then correct any syntax errors in <code>sec.conf</code>.
<pre>sec.pl --conf=/usr/local/etc/sec.conf -testonly</pre>
<h2>Configuring syslog-ng</h2>
The following <em>example</em> is a minimal <code>syslog-ng.conf</code> that demonstrates how to send syslog events at level “error” and higher to SEC:
<pre>source s_all {
  tcp();
  udp();
  internal();
  unix-stream("/dev/log");
  file("/proc/kmsg" program_override("kernel: "));
};
filter f_problems { level("err") or level("alert") or level("crit") or level("emerg"); };
destination d_sec {
  program("/usr/local/sbin/sec.pl -input="-" -conf=/usr/local/etc/sec.conf");
};
log {
  source(s_all);
  filter(f_problems);
  destination(d_sec);
}</pre>
<h2>Restarting syslog-ng</h2>
You will restart syslog-ng in order to have changes to your SEC config file take effect.
<h2>Next Steps</h2>
The example <code>sec.conf</code> I listed above will notify you about <em>all</em> errors in your network. You will certainly want to add more "Suppress" rules and other rules to collect and report errors (see the SEC manpage and FAQ for tips).