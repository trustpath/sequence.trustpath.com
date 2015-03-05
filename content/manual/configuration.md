+++
date = "2015-02-28T18:48:24-08:00"
title = "Configuration"
+++

```
version = "0.1"

timeFormats = [
	"Mon Jan _2 15:04:05 2006",
	"Mon Jan _2 15:04:05 MST 2006",
	"Mon Jan 02 15:04:05 -0700 2006",
	"02 Jan 06 15:04 MST",
	"02 Jan 06 15:04 -0700",
	"Monday, 02-Jan-06 15:04:05 MST",
	"Mon, 02 Jan 2006 15:04:05 MST",
	"Mon, 02 Jan 2006 15:04:05 -0700",
	"2006-01-02T15:04:05Z07:00",
	"2006-01-02T15:04:05.999999999Z07:00",
	"Jan _2 15:04:05",
	"Jan _2 15:04:05.000",
	"Jan _2 15:04:05.000000",
	"Jan _2 15:04:05.000000000",
	"_2/Jan/2006:15:04:05 -0700",
	"Jan 2, 2006 3:04:05 PM",
	"Jan 2 2006 15:04:05",
	"Jan 2 15:04:05 2006",
	"Jan 2 15:04:05 -0700",
	"2006-01-02 15:04:05,000 -0700",
	"2006-01-02 15:04:05 -0700",
	"2006-01-02 15:04:05-0700",
	"2006-01-02 15:04:05,000",
	"2006-01-02 15:04:05",
	"2006/01/02 15:04:05",
	"06-01-02 15:04:05,000 -0700",
	"06-01-02 15:04:05,000",
	"06-01-02 15:04:05",
	"06/01/02 15:04:05",
	"15:04:05,000",
	"1/2/2006 3:04:05 PM",
	"1/2/06 3:04:05.000 PM",
	"1/2/2006 15:04",
	"02Jan2006 03:04:05",
	"Jan _2, 2006 3:04:05 PM",
	"2006-01-02T15:04:05Z",
	"2006-01-02T15:04:05-0700",
	"2006-01-02T15:04:05.999-0700",
	"2006-01-02",
	"15:04:05",
	"2006-01-02T15:04:05.999999Z",
	"02/Jan/2006:15:04:05.999"
]

fields = [
	"msgid:string",				# The message identifier
	"msgtime:time",				# The timestamp that’s part of the log message
	"severity:integer",			# The severity of the event, e.g., Emergency, …
	"priority:integer",			# The pirority of the event
	"apphost:string",			# The hostname of the host where the log message is generated
	"appip:ipv4",				# The IP address of the host where the application that generated the log message is running on.
	"appvendor:string",			# The type of application that generated the log message, e.g., Cisco, ISS
	"appname:string",			# The name of the application that generated the log message, e.g., asa, snort, sshd
	"srcdomain:string",			# The domain name of the initiator of the event, usually a Windows domain
	"srczone:string",			# The originating zone
	"srchost:string",			# The hostname of the originator of the event or connection.
	"srcip:ipv4",				# The IPv4 address of the originator of the event or connection.
	"srcipnat:ipv4",			# The natted (network address translation) IP of the originator of the event or connection.
	"srcport:integer",			# The port number of the originating connection.
	"srcportnat:integer",		# The natted port number of the originating connection.
	"srcmac:mac",				# The mac address of the host that originated the connection.
	"srcuser:string",			# The user that originated the session.
	"srcuid:integer",			# The user id that originated the session.
	"srcgroup:string",			# The group that originated the session.
	"srcgid:integer",			# The group id that originated the session.
	"srcemail:string",			# The originating email address
	"dstdomain:string",			# The domain name of the destination of the event, usually a Windows domain
	"dstzone:string",			# The destination zone
	"dsthost:string",			# The hostname of the destination of the event or connection.
	"dstip:ipv4",				# The IPv4 address of the destination of the event or connection.
	"dstipnat:ipv4",			# The natted (network address translation) IP of the destination of the event or connection.
	"dstport:integer",			# The destination port number of the connection.
	"dstportnat:integer",		# The natted destination port number of the connection.
	"dstmac:mac",				# The mac address of the destination host.
	"dstuser:string",			# The user at the destination.
	"dstuid:integer",			# The user id that originated the session.
	"dstgroup:string",			# The group that originated the session.
	"dstgid:integer",			# The group id that originated the session.
	"dstemail:string",			# The destination email address
	"protocol:string",			# The protocol, such as TCP, UDP, ICMP, of the connection
	"iniface:string",			# The incoming interface
	"outiface:string",			# The outgoing interface
	"policyid:integer",			# The policy ID
	"sessionid:integer",		# The session or process ID
	"object:string",			# The object affected.
	"action:string",			# The action taken
	"command:string",			# The command executed
	"method:string",			# The method in which the action was taken, for example, public key or password for ssh
	"status:string",			# The status of the action taken
	"reason:string",			# The reason for the action taken or the status returned
	"bytesrecv:integer",		# The number of bytes received
	"bytessent:integer",		# The number of bytes sent
	"pktsrecv:integer",			# The number of packets received
	"pktssent:integer",			# The number of packets sent
	"duration:integer"			# The duration of the session
]

[analyzer]
	[analyzer.prekeys]
	address		= [ "srchost", "srcipv4" ]
	by 			= [ "srchost", "srcipv4", "srcuser" ]
	command 	= [ "command" ]
	connection 	= [ "sessionid" ]
	dport		= [ "dstport" ]
	dst 		= [ "dsthost", "dstipv4" ]
	duration	= [ "duration" ]
	egid 		= [ "srcgid" ]
	euid 		= [ "srcuid" ]
	for 		= [ "srchost", "srcipv4", "srcuser" ]
	from 		= [ "srchost", "srcipv4" ]
	gid 		= [ "srcgid" ]
	group 		= [ "srcgroup" ]
	logname 	= [ "srcuser" ]
	port 		= [ "srcport", "dstport" ]
	proto		= [ "protocol" ]
	rhost 		= [ "srchost", "srcipv4" ]
	ruser 		= [ "srcuser" ]
	sport		= [ "srcport" ]
	src 		= [ "srchost", "srcipv4" ]
	time 		= [ "msgtime" ]
	to 			= [ "dsthost", "dstipv4", "dstuser" ]
	uid 		= [ "srcuid" ]
	uname 		= [ "srcuser" ]
	user 		= [ "srcuser" ]

	[analyzer.keywords]
	action = [
		"access",
		"alert",
		"allocate",
		"allow",
		"audit",
		"authenticate",
		"backup",
		"bind",
		"block",
		"build",
		"built",
		"cancel",
		"clean",
		"close",
		"compress",
		"connect",
		"copy",
		"create",
		"decode",
		"decompress",
		"decrypt",
		"depress",
		"detect",
		"disconnect",
		"download",
		"encode",
		"encrypt",
		"establish",
		"execute",
		"filter",
		"find",
		"free",
		"get",
		"initialize",
		"initiate",
		"install",
		"lock",
		"login",
		"logoff",
		"logon",
		"logout",
		"modify",
		"move",
		"open",
		"post",
		"quarantine",
		"read",
		"release",
		"remove",
		"replicate",
		"resume",
		"save",
		"scan",
		"search",
		"start",
		"stop",
		"suspend",
		"teardown",
		"uninstall",
		"unlock",
		"update",
		"upgrade",
		"upload",
		"violate",
		"write"
	]

	status = [
		"accept",
		"error",
		"fail",
		"failure",
		"success"
	]

	object = [
		"account",
		"app",
		"bios",
		"driver",
		"email",
		"event",
		"file",
		"flow",
		"connection",
		"memory",
		"packet",
		"process",
		"rule",
		"session",
		"system",
		"thread",
		"vuln"
	]

	srcuser = [
		"root",
		"admin",
		"administrator"
	]

	method = [
		"password",
		"publickey"
	]

	protocol = [
		"udp",
		"tcp",
		"icmp",
		"http/1.0",
		"http/1.1"
	]
```