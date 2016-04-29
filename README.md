# internetday2016

[1001](internetday)
secret=1001

[1002](internetday)
secret=1002



[internetday]
exten => 1001,1,Dial(SIP/1001)
exten => 1002,1,Dial(SIP/1002)


[ivr-demo]
;
; Example "main menu" context with submenu
;
exten => s,1,Answer
 same => n,Background(dir-welcome)
 same => n,Background(demo-instruct)            ; "Thanks for calling press 1 for sales, 2 for support, ..."
 same => n,WaitExten(10)

exten => 1,1,Goto(ivr-submenu,s,1)
exten => 2,1,Hangup
;
[ivr-submenu]
exten => s,1,NoOP()                                     ; Make them comfortable with 2 seconds of ringback
 same => n,Background(demo-congrats)    ; "Thanks for calling the sales department.  Press 1 for steve, 2 for..."
 same => n,WaitExten(5)
exten => 1,1,Goto(internetday,1001,1)
exten => 2,1,Goto(internetday,1002,1)

register => 5406046692:xxxxxxxx@sip.messagenet.it:5061/1001

[sip.messagenet.it](siptrunk)          ; outgoing calls
username = 5406046692
fromuser = 5406046692
secret = xxxxxxxxx
host = sip.messagenet.it
port = 5061

exten => 1003,1,NoOP(Numero chiamante ${CALLERID(num):3})
same => n,GotoIf($[${CALLERID(num):3} == 3493824114]?monkeys)
same => n,Dial(SIP/1001)
same => n,Hangup()
same => n(monkeys),Playback(tt-monkeys)
same => n,Hangup()
