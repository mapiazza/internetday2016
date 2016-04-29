# Internetday 2016


## Pre-requisiti

- Macchina linux installata, nel caso della demo Ubuntu server
- pacchetto asterisk installato, nel caso di debian/ubuntu apt-get install asterisk
- Microsip: [http://www.microsip.org](https://www.microsip.org)
- Documentazione asterisk: [https://wiki.asterisk.org/](https://wiki.asterisk.org/)
- Account Messagenet: [http://www.messagenet.com/](http://www.messagenet.com/)
- Se non si disponde di un server linux hardware, Amazon AWS: [http://aws.amazon.com](http://aws.amazon.com)

## Laboratorio

Aggiungo due device alla configurazione del file /etc/asterisk/sip.conf

```
[1001](internetday)
secret=1001

[1002](internetday)
secret=1002
```



Aggiungo due regole al dialplan, nel file /etc/asterisk/extensions.conf aggiungo

```
[internetday]
exten => 1001,1,Dial(SIP/1001)
exten => 1002,1,Dial(SIP/1002)
```
Configuro due sofphone sip [www.microsip.org](https://www.microsip.org)


Creo un risponditore automatico, nel file /etc/asterisk/extensions.conf aggiungo 

```
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
```

Aggiungo un sip trunk tramite un provider, in questo caso messagenet, nel file sip.conf aggiungo

```
register => 5406046692:xxxxxxxx@sip.messagenet.it:5061/1001

[sip.messagenet.it](siptrunk)          ; outgoing calls
username = 5406046692
fromuser = 5406046692
secret = xxxxxxxxx
host = sip.messagenet.it
port = 5061
```

Monkeys! Il mio vicino mi disturba, aggingiamo un filtro sul numero del chiamante, nel diaplan extensions.conf aggiungo 

```
exten => 1003,1,NoOP(Numero chiamante ${CALLERID(num):3})
same => n,GotoIf($[${CALLERID(num):3} == 3493824114]?monkeys)
same => n,Dial(SIP/1001)
same => n,Hangup()
same => n(monkeys),Playback(tt-monkeys)
same => n,Hangup()
```

