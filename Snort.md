# Snort Configuration for Windows

## What is Snort

Snort is a Free and Open Source Network Intrusion Prevention and  Detection System. It uses a rule-based language combining signature, protocol and anomaly inspection methods to detect malicious activity such as DOS attacks, Buffer overflows, stealth port scans, CGI attacks, SMB probes, OS fingerprinting attempts, and more. It is capable of performing real time traffic analysis and packet logging on IP networks.

## The following document helps in configuring Snort in Intrusion Detection mode

Step 1: Install Snort with default configuration and default path which is C:\Snort

Step 2 : Open CMD as admin at location c:\Snort\bin and run following command to check if it is installed correctly.

```snort
snort -W
```

The above code give a list of adapters with numbers. Choose the one to monitor and use that number in next command

```snort
snort -i 1 -dev
```

If it runs successfully, then its installed succesfully

Step 3 : Add Downloaded rules to the rules folder in the way you like. It is optional and can be done later but it is recommended to add the basic community rules provided by community.

> Snort configuration file is snort.conf and is present in C:\Snort\etc\ . Make sure to back it up before making any changes.

Step 4 : In snort.conf file replace 11 instances of "ipvar" by "var". You can do this in any editor which have find and replace function and save it.

Step 5 : In snort.conf, find the line at number 247 which has path to dynamic preprocessor libraries and replace the following line by path to all the libraries present in the snort_dynamicpreprocessor folder.
replace
>dynamicpreprocessor directory /usr/local/lib/snort_dynamicpreprocessor/

to

>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_dce2.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_dnp3.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_dns.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_ftptelnet.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_gtp.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_imap.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_modbus.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_pop.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_reputation.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_sdf.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_sip.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_smtp.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_ssh.dll  
>dynamicpreprocessor C:\Snort\lib\snort_dynamicpreprocessor\sf_ssl.dll  

Step 6 : In snort.conf, replace line number 263 having path to base preprocessor engine, from
>dynamicengine /usr/local/lib/snort_dynamicengine/libsf_engine.so

to

>dynamicengine C:\Snort\lib\snort_dynamicengine\sf_engine.dll

Step 7 : Add a directory named "snort_dynamicrules" in "C:\Snort\lib\" and replace line number 266 in snort.conf having path to dynamic rules libraries, from
>dynamicdetection directory /usr/local/lib/snort_dynamicrules

to

>dynamicdetection directory C:\Snort\lib\snort_dynamicrules\

Step 7 : In snort.conf, comment the following lines which are at number 278-282
>\#preprocessor normalize_ip4  
>\#preprocessor normalize_tcp: ips ecn stream  
>\#preprocessor normalize_icmp4  
>\#preprocessor normalize_ip6  
>\#preprocessor normalize_icmp6  

Step 8 : Create a dummy rule file in "C:\Snort\rules\" by name "white_list.rules"

Step 9 : Create a dummy rule file in "C:\Snort\rules\" by name "black_list.rules"

Step 10 : In snort.conf, change following lines at number 104, from
>var RULE_PATH ../rules  
>var SO_RULE_PATH ../so_rules  
>var PREPROC_RULE_PATH ../preproc_rules  

to

>var RULE_PATH C:\Snort\rules  
>var SO_RULE_PATH C:\Snort\so_rules   
>var PREPROC_RULE_PATH C:\Snort\preproc_rules  

Step 11 : In snort.conf, change following lines at number 113, from
>var WHITE_LIST_PATH ../rules  
>var BLACK_LIST_PATH ../rules  

to

>var WHITE_LIST_PATH ..\rules  
>var BLACK_LIST_PATH ..\rules  

Step 12 : In snort.conf, change following lines at number 523, from
>whitelist $WHITE_LIST_PATH/white_list.rules, \  
>blacklist $BLACK_LIST_PATH/black_list.rules  

to

>whitelist $WHITE_LIST_PATH\white_list.rules, \  
>blacklist $BLACK_LIST_PATH\black_list.rules  

Step 13 : In snort.conf, change following lines at number 571, from
>include $RULE_PATH/blacklist.rules

to

>include $RULE_PATH/black_list.rules

Step 14: Run following command to test if configuration is done properly.

```snort
snort -i 1 -c C:\Snort\etc\snort.conf -l C:\Snort\log -T
```

If it is validated successfully, then configuration is done successfully.

Now you can write new Snort rules in a ".rules" file and save it in "C:\Snort\rules" directory. Add its path to "snort.conf" file under "Step 7" section of that configuration file for Snort to recognize your custom rule.
Snort rule goes like-
>alert    ip      any          any      ->    any       any        (msg: "IP Packet Found: "; sid:        36666666;)
>act     proto  SourceIP   Source Port      Dest.IP  Dest.Port                              Snort ID      any large number