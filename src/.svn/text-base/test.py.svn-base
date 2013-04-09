#!/usr/bin/python2.4
'''
Created on 2011-7-8

@author: Evil
'''

import sys, zipfile, os , commands
import sys  

reload(sys)   
sys.setdefaultencoding('utf-8')   

def unzip_file_into_dir(file, dir):
    
    if os.path.exists(dir):
        status,output = commands.getstatusoutput('rm -r '+dir)
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
        status,output = commands.getstatusoutput('/usr/bin/unzip %s -d %s'%(file,dir))
        print status
        
        if status != 0:
            return False
    
    return True
  
import time    
import random

if __name__ == '__main__':
    
    #unzip_file_into_dir('/root/apk/sjmf_mofun_v5[1].0111.apk','/root/test1212')#20100907210246368-2123738846.apk
    
    #str = '/data0/usr/Project/WebRoot_manager/temp/18/Q_1.2_2011072114471592842.apk'
    
    #print os.path.dirname(str)
    print '%s%d'%(str(time.time()).replace('.',''),random.randint(10000,99999))
    print random.randint(10000,99999)
    
    print os.path.splitext('/ddd/aaa/sdfsd/aa')[1]

    
