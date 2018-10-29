
## Asterisk to FreeSWITCH Rosetta Stone
While FreeSWITCH is not a drop-in replacement for Asterisk, it does many of the same things that Asterisk does. This page is an attempt to help those familiar with Asterisk to leverage that knowledge and quickly locate that which is equivalent or analogous in FreeSWITCH. In most cases there isn't a direct, one-to-one translation, but rather similarities. For example, the "equivalent" of extensions.conf is (mostly) conf/dialplan/default.xml; but there are also features.xml, public.xml and /conf/dialplan/extensions/*xml.

* **If anyone has thoughts on this then by all means add what you know.**

### Configuration Files

 Asterisk | FreeSWITCH 
:- | :- 
 extensions.conf | conf/dialplan/default.xml; also features.xml, public.xml, extensions/*xml 
 logger.conf | mod_console and mod_syslog 
 rtp.conf | conf/autoload_configs/switch.conf.xml 
 sip.conf | conf/directory/*.xml (see mod_sofia) 
 voicemail.conf | mod_voicemail - voicemail.conf.xml, conf/directory/*xml 
 zapata.conf | conf/autoload_configs/openzap.conf.xml 
 Realtime | Use mod_xml_curl to fetch the user and/or dialplan in XML,mod_ldap for LDAP backend 

### Console Commands

 Asterisk Console | FreeSWITCH Fs cli 
 :- | :- 
 sip show peers/sip show registry | sofia status profile internal  
 core set verbose 9	| /log 7 
 core set debug 9 | /debug 7 
 core show channels	| show channels / show calls 
 reload | reloadxml 
 hangup request <channel> | uuid_kill <uuid> 
 sip reload | sofia profile internal rescan 
 sip set debug on | sofia global siptrace on <br> sofia global debug (presence&#124;sla&#124;none) <br> sofia loglevel all [0-9]
 sip set debug (ip&#124;peer) | sofia profile (internal&#124;external) siptrace on 
 module load app_queue.so | load mod_callcenter 
 core show uptime | status 
 core show version | version 
 console dial 1000 | pa call 1000 (see mod_portaudio) 


### Miscellaneous

Asterisk | FreeSWITCH
:- | :-
AMI | mod_event_socket
asterisk -r	| fs_cli
asterisk -rx "command" | fs_cli -x "command"
set verbose <verbosity> in CLI | console loglevel 0-8 or console loglevel debug
chan_local | Loopback
stop gracefully | shutdown or ...

### sip.conf params
Asterisk | FreeSWITCH
:- | :-
dtmfmode | In dialplan: start_dtmf

Asterisk experts: please add more information


### Dialplan
Asterisk | FreeSWITCH
:- | :-
exten => | <extension></extension> tags
include => | Misc._Dialplan_Tools_transfer
Realtime | Mod xml curl to fetch the dialplan in XML
Answer | Misc._Dialplan_Tools_answer
AGI | Event Socket Outbound
Background | Usually used for:<br>    IVR<br>    play_and_get_digits
ChanSpy	| Misc._Dialplan_Tools_eavesdrop
Dial | see bridge app
Dial(&#124;&#124;L(x[:y][:z]) | Limiting call time, use sched_hangup for the x and sched_broadcast for the :y and :z
Dial(SIP/${EXTEN}/sipuser) | bridge with data="{sip_route_uri=sipuser}user/whatever" or data="sofia/whatever%domain.com^sipuser"
DumpChan | Misc._Dialplan_Tools_info
Echo | Misc._Dialplan_Tools_echo
Goto | Misc._Dialplan_Tools_transfer
GotoIf | Conditions in dialplan (&lt;condition field="blah" expression="foo"&gt;)
Hangup | Misc._Dialplan_Tools_hangup
Log | Misc._Dialplan_Tools_log
Macro/GoSub | Misc._Dialplan_Tools_execute_extension
MeetMe | mod_conference
Monitor | Misc._Dialplan_Tools_record_session
Monitor_exec | Channel_Variables#api_hangup_hook
MP3Player | mod_shout
Musiconhold | mod_local_stream
NoCDR | &lt;action application="set" data="process_cdr=false"/&gt;
NoOp | Usually used for logging - Misc._Dialplan_Tools_log
Park | Misc._Dialplan_Tools_park
Playback | Misc._Dialplan_Tools_playback
Playtones | Misc._Dialplan_Tools_gentones
Progress | Misc._Dialplan_Tools_pre_answer
Queue | mod_fifo
Read | Misc._Dialplan_Tools_read
Record | Misc._Dialplan_Tools_record
Set | Misc._Dialplan_Tools_set
SetGlobal | Misc._Dialplan_Tools_set_global
SIPGetHeader | Auto set as variable - ${sip_h_HEADER} where HEADER is the header name
SIPAddHeader | Set variable ${sip_h_HEADER} where HEADER is the header name you want to send
System | Misc._Dialplan_Tools_system
Transfer | Misc._Dialplan_Tools_redirect
Wait | Misc._Dialplan_Tools_sleep
WaitExten | Misc._Dialplan_Tools_play_and_get_digits