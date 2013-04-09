#!/usr/bin/env python
#coding:utf-8 
# --*-- encoding:utf-8 --*--
'''
Created on 2011-7-14

@author: Evil

export LANG="zh_CN.GB18030"
'''

import commands
import hashlib
import StringIO
import sys

from utils import *

reload(sys)   
sys.setdefaultencoding('utf-8')  

#RSAFile like: /root/apk/20110705144927058251126935.apk-unpack/META-INF/WORKNET.RSA
def getSignMD5(RSAFile) :
    signBase64Str = commands.getoutput('openssl pkcs7 -in '+convertFilePath(RSAFile)+' -inform DER -print_certs')
    signBase64StringIO = StringIO.StringIO()
    
    lines = signBase64Str.splitlines()
    flag = False
    for index ,line in enumerate(lines):
        if cmp('-----BEGIN CERTIFICATE-----',line) == 0:
            flag = True
            continue
        elif cmp('-----END CERTIFICATE-----',line) == 0:
            flag = False
            continue
            
        if flag == True:
            signBase64StringIO.write(line)
    
    m = hashlib.md5(signBase64StringIO.getvalue())
    m.digest()
    return m.hexdigest()
    

if __name__ == '__main__': getSignMD5(sys.argv[1])
    

