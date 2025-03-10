# 서버 TLS 인증서 확인 
```
ubuntu@bastion:~$ openssl s_client -connect ipa.cheil-tech.com:636 -showcerts
CONNECTED(00000003)
depth=1 O = CHEIL-TECH.COM, CN = Certificate Authority
verify return:1
depth=0 O = CHEIL-TECH.COM, CN = ipa.cheil-tech.com
verify return:1
---
Certificate chain
 0 s:O = CHEIL-TECH.COM, CN = ipa.cheil-tech.com
   i:O = CHEIL-TECH.COM, CN = Certificate Authority
   a:PKEY: rsaEncryption, 2048 (bit); sigalg: RSA-SHA256
   v:NotBefore: Feb 19 03:08:00 2024 GMT; NotAfter: Feb 19 03:08:00 2026 GMT
-----BEGIN CERTIFICATE-----

...

-----END CERTIFICATE-----
---
Server certificate
subject=O = CHEIL-TECH.COM, CN = ipa.cheil-tech.com
issuer=O = CHEIL-TECH.COM, CN = Certificate Authority
---
Acceptable client certificate CA names
O = CHEIL-TECH.COM, CN = Certificate Authority
Requested Signature Algorithms: ECDSA+SHA256:ECDSA+SHA384:ECDSA+SHA512:ECDSA+SHA1:RSA-PSS+SHA256:RSA-PSS+SHA384:RSA-PSS+SHA512:RSA+SHA256:RSA+SHA384:RSA+SHA512:RSA+SHA1
Shared Requested Signature Algorithms: ECDSA+SHA256:ECDSA+SHA384:ECDSA+SHA512:RSA-PSS+SHA256:RSA-PSS+SHA384:RSA-PSS+SHA512:RSA+SHA256:RSA+SHA384:RSA+SHA512
Peer signing digest: SHA256
Peer signature type: RSA-PSS
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 3126 bytes and written 414 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_128_GCM_SHA256
Server public key is 2048 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
```
# LDAP 이상여부 확인
```
ubuntu@repo:~$ sudo gitlab-rake gitlab:ldap:check

Checking LDAP ...

  

LDAP: ... Server: ldapmain

LDAP authentication... Success

LDAP users with access to your GitLab server (only showing the first 100 results)

DN: uid=cycle.lee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: cycle.lee

DN: uid=jayong.hur,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jayong.hur

DN: uid=jychris.yang,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jychris.yang

DN: uid=inpyeong.lee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: inpyeong.lee

DN: uid=junivers.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: junivers.kim

DN: uid=sangmok.yun,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: sangmok.yun

DN: uid=swan.song,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: swan.song

DN: uid=ukgyum.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: ukgyum.kim

DN: uid=yj320.shin,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: yj320.shin

DN: uid=external.test,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: external.test

DN: uid=jace.heo,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jace.heo

DN: uid=derek.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: derek.kim

DN: uid=jk1.yang,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jk1.yang

DN: uid=bob1.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: bob1.kim

DN: uid=nekrahur,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: nekrahur

DN: uid=haloc74,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: haloc74

DN: uid=jenkins.devops,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jenkins.devops

DN: uid=nicefunk,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: nicefunk

DN: uid=shkim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: shkim

DN: uid=lwg87,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: lwg87

DN: uid=softdev,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: softdev

DN: uid=hangik.lee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hangik.lee

DN: uid=byy,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: byy

DN: uid=dahye.choi,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: dahye.choi

DN: uid=hskim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hskim

DN: uid=sjkang,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: sjkang

DN: uid=eunseop.im,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: eunseop.im

DN: uid=chanu75.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: chanu75.ericcnc

DN: uid=ech.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: ech.ericcnc

DN: uid=csm.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: csm.ericcnc

DN: uid=shp.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: shp.ericcnc

DN: uid=kji.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kji.ericcnc

DN: uid=mjy.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: mjy.ericcnc

DN: uid=jjh.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jjh.ericcnc

DN: uid=kjh.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kjh.ericcnc

DN: uid=jjy.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jjy.ericcnc

DN: uid=swj.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: swj.ericcnc

DN: uid=ksh.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: ksh.ericcnc

DN: uid=kim.allan,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kim.allan

DN: uid=twih1203,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: twih1203

DN: uid=dew.hanbom,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: dew.hanbom

DN: uid=idowww.hanbom,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: idowww.hanbom

DN: uid=anna1.yu,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: anna1.yu

DN: uid=kjh5835,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kjh5835

DN: uid=chanju.hwang,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: chanju.hwang

DN: uid=sjgains,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: sjgains

DN: uid=wishinghyun,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: wishinghyun

DN: uid=h69088,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: h69088

DN: uid=fujeong00,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: fujeong00

DN: uid=skk,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: skk

DN: uid=devops.test,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: devops.test

DN: uid=yubin,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: yubin

DN: uid=prlee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: prlee

DN: uid=jungbin.won,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jungbin.won

DN: uid=sean1.han,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: sean1.han

DN: uid=yjng.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: yjng.kim

DN: uid=yschoi,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: yschoi

DN: uid=shoh,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: shoh

DN: uid=ivy626,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: ivy626

DN: uid=tw.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: tw.kim

DN: uid=shutdown38,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: shutdown38

DN: uid=senna.r,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: senna.r

DN: uid=kim.dsouza,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kim.dsouza

DN: uid=boodas,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: boodas

DN: uid=jungwoo,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jungwoo

DN: uid=kangyub,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: kangyub

DN: uid=jaeseong,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jaeseong

DN: uid=hyeokjae,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hyeokjae

DN: uid=taehee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: taehee

DN: uid=sidn,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: sidn

DN: uid=chlwkfyd,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: chlwkfyd

DN: uid=stolenfish,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: stolenfish

DN: uid=smpa389,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: smpa389

DN: uid=hyoungsang,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hyoungsang

DN: uid=seungjin,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: seungjin

DN: uid=ksyoon,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: ksyoon

DN: uid=gooseul.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: gooseul.kim

DN: uid=jaesung.park,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jaesung.park

DN: uid=maisie.seong,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: maisie.seong

DN: uid=jh.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jh.ericcnc

DN: uid=hj.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hj.ericcnc

DN: uid=jy.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: jy.ericcnc

DN: uid=mj.ericcnc,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: mj.ericcnc

DN: uid=hye.ro,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hye.ro

DN: uid=minah526.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: minah526.kim

DN: uid=seowhan.ryu,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: seowhan.ryu

DN: uid=issuettl,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: issuettl

DN: uid=igor,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: igor

DN: uid=gh_.sung,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: gh_.sung

DN: uid=lkckss1234,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: lkckss1234

DN: uid=yang.jaewoong7471,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: yang.jaewoong7471

DN: uid=limhanhiro,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: limhanhiro

DN: uid=qmffor09.kim,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: qmffor09.kim

DN: uid=dongjun.cho,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: dongjun.cho

DN: uid=mylee,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: mylee

DN: uid=takeej,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: takeej

DN: uid=hmyi,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: hmyi

DN: uid=raymond,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: raymond

DN: uid=mohit.kapoor,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: mohit.kapoor

DN: uid=v.arunkumar,cn=users,cn=accounts,dc=cheil-tech,dc=com uid: v.arunkumar

  

Checking LDAP ... Finished
```

# psql collation 불일치 여부 확인
```zsh
sudo gitlab-psql

gitlabhq_production=#
SELECT collname AS COLLATION_NAME,
       collversion AS VERSION,
       pg_collation_actual_version(oid) AS actual_version
FROM pg_collation
WHERE collprovider = 'c';

```

```sql
# 정상
 collation_name | version | actual_version 
----------------+---------+----------------
 C              |         | 
 POSIX          |         | 
 ucs_basic      |         | 
 C.UTF-8        |         | 
 en_US.utf8     | 2.31    | 2.31
 en_US          | 2.31    | 2.31
(6 rows)

# 불일치
 collation_name | version | actual_version
----------------+---------+----------------
 C              |         |
 POSIX          |         |
 ucs_basic      |         |
 C.utf8         |         |
 en_US.utf8     | 2.27    | 2.35
 en_US          | 2.27    | 2.35
(6 rows)
```