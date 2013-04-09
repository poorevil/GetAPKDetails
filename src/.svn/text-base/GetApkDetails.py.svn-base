#!/usr/bin/env python
#coding:utf-8 
# --*-- encoding:utf-8 --*--
'''
Created on 2011-7-7

@author: Evil

export LANG="zh_CN.GB18030"
'''

import sys
import json
import commands
import re
import os
import traceback 
import time    
import random


from UnpackAPK import *
from xml.sax.handler import *
from XMLParser import *
from GetSignMD5 import *
from utils import *

reload(sys)   
sys.setdefaultencoding('utf-8')   

apkFilePath = ''    #apk file path
unpackPath = ''     #apk unpack file path

def getAppNameAndAppIcon(apkFilePath,unpackPath):
    
    xmlStr = commands.getoutput('java -jar /usr/local/GetApkDetails/AXMLPrinter2.jar '+convertFilePath(unpackPath)+'/AndroidManifest.xml')

    sid,iconId,pkg,versionCode,versionName = QuotationHandler().parseXML(xmlStr)#get nameID and iconID
    
    output = commands.getoutput('aapt dump --values resources '+convertFilePath(apkFilePath))
    lines = output.splitlines()

    appNameArray = []  #app name array
    iconFileArray = [] #app icon file array

    appNameArray = getAppName(lines , sid , idx = [-1]);
    
    versionName = getAppName(lines , versionName , idx = [-1])[0]

    for index,line in enumerate(lines):
        if line.find('spec resource') == -1 :
            '''
            if sid.find('@') > -1 and line.find(str(sid.lower()[1:])) > -1 :
                
                (reference) 0x7f09004
                
                appName = unicode(lines[index+1],'utf-8')
                appNameArray.append(appName[appName.find('\"')+1:appName.rfind('\"')])
            elif 
            '''
            if line.find(str(str(iconId).lower()[1:])) > -1 :
                #165:      spec resource 0x7f02005e com.zhancheng.android.daomu:drawable/icon: flags=0x00000000
                #788:        resource 0x7f02005e com.zhancheng.android.daomu:drawable/icon: t=0x03 d=0x0000005e (s=0x0008 r=0x00)
                #                (string8) "res/drawable/icon.png"
                #iconFile = lines[index+1][line.find(pkg)+len(pkg)+1:line.rfind(':')]
                
                iconLine = lines[index+1]
                iconFile = iconLine[iconLine.find('\"')+1:iconLine.rfind('\"')]

                iconFileArray = getIconArray(os.path.basename(iconFile),unpackPath)
                break
    '''
    if sid.find('@') == -1:
        appNameArray.append(unicode(str(sid),'utf-8'))
    '''
    
    return appNameArray , iconFileArray , pkg , versionCode , versionName

def getAppName(lines , sid , idx = [-1]):
    appNameArray = []
    
    if sid is not None:
        if sid.find('@') > -1 :
            #if len(lines) > 0:
                for index,line in enumerate(lines):
                    if line.find('spec resource') == -1 and isIdxInArray(idx , index) == False:
                        if line.find(str(sid.lower()[1:])) > -1 :
                            appName = unicode(lines[index+1],'utf-8')
                            
                            if appName.find('reference') > -1 :
                                if cmp(sid,str(appName[appName.find('0x'):]).replace('0x', '@')) == 0:
                                    idx.append(index)
                                    idx.append(index+1)
                                    appNameArray = getAppName(lines , sid , idx)
                                else :
                                    appNameArray = getAppName(lines , str(appName[appName.find('0x'):]).replace('0x', '@'))
                            else :
                                appNameArray.append(appName[appName.find('\"')+1:appName.rfind('\"')])
        else :
            appNameArray.append(unicode(str(sid),'utf-8'))
    
    return appNameArray

def isIdxInArray(array , idx):
    for num in array:
        if cmp(str(num),str(idx)) == 0:
            return True
    
    return False



import os,glob

def getIconArray(iconFileName,unpackPath):
    iconArray = []

    for root, dirs, files in os.walk(unpackPath): 
        '''
        print os.path.join(root, iconFileName)
        result = glob.glob(os.path.join(root, iconFileName)) 
        print result
        for f in result:
           iconArray.append(f)
        '''
        for file in files :
            if cmp(iconFileName,file) == 0:
                iconArray.append(os.path.join(root, file))
        
    return iconArray

def getOtherDetails(apkFilePath,unpackPath):
    output = commands.getoutput('aapt dump  badging '+convertFilePath(apkFilePath))
    lines = output.splitlines()
    
    ''' uses-permission Array '''
    usesPermissionArray = []
    ''' sdk version '''
    sdkVersion = ''
    ''' localesArray '''
    localesArray = []
    ''' icon file name '''
    #iconFileArray = []
    
    for line in lines:
        if line.find('uses-permission:\'') > -1 :
            usesPermissionArray.append(line[line.find('\'')+1:line.rfind('\'')])
        elif line.find('sdkVersion:\'') > -1 :
            sdkVersion = line[line.find('\'')+1:line.rfind('\'')] #get sdkVersion
        elif line.find('locales:') > -1 :
            localesArray = line[line.find(':')+2:].split(' ')
        #elif line.find('application:') > -1 :
            #application: label='Maps' icon='res/drawable-160dpi/ic_launcher_maps.png'
            #iconFileArray = getIconArray(os.path.basename(line[line.find('icon=\'')+6:line.rfind('\'')]),unpackPath)
    
    ''' file size '''
    fileSize = str(os.path.getsize(apkFilePath)/1000.0/1000.0)+'MB'
    
    ''' signatures MD5 string'''
    signaturesMD5 = ''
    
    files = os.listdir(os.path.join(unpackPath, 'META-INF/'))
    for f in files:
        if f.endswith('.RSA') or f.endswith('.DSA') :
                signaturesMD5 = getSignMD5(os.path.join(unpackPath, 'META-INF/',f))

    return usesPermissionArray , sdkVersion , localesArray , fileSize , signaturesMD5

#获取apk详细信息
def getApkDetails(apkFilePath,unpackPath):
    apkDetails = {}
    
    apkDetails['apkFileName'] = os.path.basename(apkFilePath)
    apkDetails['apkFilePath'] = apkFilePath
    
    try:
        ''' unpack apk file '''
        unzip_file_into_dir(apkFilePath, unpackPath)
    
    
        ''' get app name '''
        appNameArray , iconFileArray , pkg , versionCode , versionName = getAppNameAndAppIcon(apkFilePath,unpackPath)
        apkDetails['appNameArray'] = appNameArray
        
        ''' get other all details '''
        usesPermissionArray , sdkVersion , localesArray , fileSize , signaturesMD5 = getOtherDetails(apkFilePath,unpackPath)
        
        '''
        for name in appNameArray:
            print name
        '''
        
        #print usesPermissionArray 
        apkDetails['usesPermissionArray'] = usesPermissionArray
        
        #print sdkVersion 
        apkDetails['sdkVersion'] = sdkVersion
        
        #print pkg 
        apkDetails['pkg'] = pkg
        
        #print versionCode 
        apkDetails['versionCode'] = versionCode
        
        #print versionName 
        apkDetails['versionName'] = versionName
        
        #print localesArray
        apkDetails['localesArray'] = localesArray
         
        #print fileSize 
        apkDetails['fileSize'] = fileSize
        
        #print iconFileArray
        apkDetails['iconFileArray'] = iconFileArray
        
        apkDetails['signaturesMD5'] = signaturesMD5
    
           
    except:
        pass
    '''
    except Exception,   e:
        exstr   =   traceback.format_exc()
        print   exstr 
    ''' 
    
    return apkDetails
    
def printHelp():
    print u'缺少参数\r\n目前支持的功能:\r\n单apk文件解析:python <base-path>/GetApkDetails <apk file path>\r\n多apk文件解析:python <base-path>/GetApkDetails <apk filedir path>\r\n文件删除:python <base-path>/GetApkDetails -D <file path>'

#程序主方法
def main():    
    if len(sys.argv) < 2 :#若命令参数小于2
        printHelp()
    elif len(sys.argv) >3 :#若命令参数大于3
        printHelp()
    else :
        if len(sys.argv) == 3 : #删除文件
            oper = sys.argv[1];
            if cmp(oper,'-D') != 0:
                printHelp()
                return 2
            
            destFilePath = sys.argv[2];#需要删除的文件路径
        
            status,output = commands.getstatusoutput('rm -rf '+convertFilePath(destFilePath))
            
            return status
            '''    
        if len(sys.argv) == 3 : #删除文件
            apkFilePath = sys.argv[1];
            unpackPath = sys.argv[2];
        
            apkDetails = getApkDetails(apkFilePath,unpackPath)
            apkDetailsArray.append(apkDetails)
            '''
        elif len(sys.argv) == 2 :#解包
            apkFileOrDir = unicode(sys.argv[1] , "gbk") ;
            
            if os.path.exists(apkFileOrDir) != True:
                printHelp()
                return 2
            
            apkDetailsArray = [] #返回的json对象
            
            if os.path.isdir(apkFileOrDir): #目录
                for root, dirs, files in os.walk(apkFileOrDir): 
                    for f in files:
                        if cmp(os.path.splitext(f)[1],'.apk') == 0 :
    
                            apkFilePath = os.path.join(root, f)
                            unpackPath = os.path.join(root, f) +'-unpack'
                            
                            apkDetails = getApkDetails(apkFilePath,unpackPath)
                            apkDetailsArray.append(apkDetails)
                
                jsonResult = json.dumps(apkDetailsArray)

                
            else: #文件
                
                if apkFileOrDir.endswith('.zip'):  #zip文件包
                    '''  支持中文目录部分
                    if unzip_file_into_dir(apkFileOrDir, apkFileOrDir+'-unzip') == True:
                        commands.getstatusoutput('rm -rf %s'%convertFilePath(apkFileOrDir))
                        
                        apkFileOrDir = apkFileOrDir+'-unzip'
                        
                        for root, dirs, files in os.walk(apkFileOrDir): 
                            for f in files:
                                if cmp(os.path.splitext(f)[1],'.apk') == 0 :
            
                                    apkFilePath = os.path.join(root, f)
                                    #文件重命名
                                    if os.path.isfile(apkFilePath) == True :
                                        apkFilePathNew = '%s_%s%d%s'%(os.path.splitext(apkFilePath)[0],str(time.time()).replace('.',''),random.randint(10000,99999),os.path.splitext(apkFilePath)[1])
                                        os.rename(apkFilePath,apkFilePathNew)
                                        apkFilePath = apkFilePathNew
                                    
                                    unpackPath = apkFilePath +'-unpack'
                                    
                                    apkDetails = getApkDetails(apkFilePath,unpackPath)
                                    apkDetailsArray.append(apkDetails)
                        
                        jsonResult = json.dumps(apkDetailsArray)

                    else :
                        printHelp()
                        return 2
                    '''
                    commands.getstatusoutput('rm -rf %s'%convertFilePath(apkFileOrDir+'-unzip'))
                    #解zip包
                    status,output = commands.getstatusoutput('/usr/bin/unzip %s -d %s'%(convertFilePath(apkFileOrDir),convertFilePath(apkFileOrDir+'-unzip')))
                    if status == 0:
                        commands.getstatusoutput('rm -rf %s'%convertFilePath(apkFileOrDir))
                        
                        apkFileOrDir = apkFileOrDir+'-unzip'
                        
                        for root, dirs, files in os.walk(apkFileOrDir): 
                            for f in files:
                                if cmp(os.path.splitext(f)[1],'.apk') == 0 :
            
                                    apkFilePath = os.path.join(root, f)
                                    #文件重命名
                                    if os.path.isfile(apkFilePath) == True :
                                        apkFilePathNew = '%s_%s%d%s'%(os.path.splitext(apkFilePath)[0],str(time.time()).replace('.',''),random.randint(10000,99999),os.path.splitext(apkFilePath)[1])
                                        os.rename(apkFilePath,apkFilePathNew)
                                        apkFilePath = apkFilePathNew
                                    
                                    unpackPath = apkFilePath +'-unpack'
                                    
                                    apkDetails = getApkDetails(apkFilePath,unpackPath)
                                    apkDetailsArray.append(apkDetails)#制定目录下所有apk解包信息数组
                        
                        jsonResult = json.dumps(apkDetailsArray)#详细信息数组转成json串

                    else :
                        printHelp()
                        return 2
                    
                else:
                
                    apkFilePath = apkFileOrDir;
                    unpackPath = apkFilePath+'-unpack';
                
                    apkDetails = getApkDetails(apkFilePath,unpackPath)
                    apkDetailsArray.append(apkDetails)
                    jsonResult = json.dumps(apkDetailsArray)
        
            
            print jsonResult  
            
        else:
            printHelp()
            return 2

if __name__ == '__main__': main()  #程序入口


