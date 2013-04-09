#!/usr/bin/env python
#coding:utf-8 
# --*-- encoding:utf-8 --*--
'''
Created on 2011-7-7

@author: Evil
解apk文件包
'''

import sys, zipfile, os , commands

from utils import *

reload(sys)   
sys.setdefaultencoding('utf-8')   

def unzip_file_into_dir(file, dir):
    
    if os.path.exists(dir):
        status,output = commands.getstatusoutput('rm -r '+convertFilePath(dir))
        ''' need check status '''
    try:
        #if not os.path.exists(dir):
        ''' create unpack path '''
        os.makedirs(dir, 0777)
        
        zfobj = zipfile.ZipFile(file)
        
        for name in zfobj.namelist():
            path = os.path.join(dir,name)
            
            if not os.path.exists(os.path.dirname(path)):
                os.makedirs(os.path.dirname(path),0777)
    
            if cmp(name, 'res') == 0:
                path = path + '_rename'
            
            if os.path.isdir(path) == False:
                outfile = open(path, 'wb')
                outfile.write(zfobj.read(name))
                outfile.close()
    
        zfobj.close()

    except:
        status,output = commands.getstatusoutput('/usr/bin/unzip %s -d %s'%(convertFilePath(file),convertFilePath(dir)))
        
        if status != 0:
            return False
    
    return True
    
if __name__ == '__main__':
    
    unzip_file_into_dir('/root/apk/sjmf_mofun_v5[1].0.apk','/root/test1212')#20100907210246368-2123738846.apk

        