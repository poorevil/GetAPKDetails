#!/usr/bin/env python
#coding:utf-8 
# --*-- encoding:utf-8 --*--
'''
Created on 2011-7-7

@author: Evil
'''

import string
import StringIO
import xml.sax
import sys  
from xml.sax.handler import *

reload(sys)   
sys.setdefaultencoding('utf-8')   


class QuotationHandler(ContentHandler):

    """Crude extractor for quotations.dtd compliant XML document"""

    def __init__(self):
        self.sid = ''
        self.iconId = ''
        self.pkg = ''
        self.versionCode = ''
        self.versionName = ''

    def startDocument(self):
        pass

    def startElement(self, name, attrs):
        if name == 'manifest':
            self.pkg = attrs.get('package')
            self.versionCode = attrs.get('android:versionCode')
            self.versionName = attrs.get('android:versionName')

        if name == 'application':
            self.sid = attrs.get('android:label')
            self.iconId = attrs.get('android:icon')

    def parseXML(self, xmlStr):
        
        parser = xml.sax.make_parser()

        handler = QuotationHandler()
    
        parser.setContentHandler(handler)
        
        #print xmlStr

        parser.parse(StringIO.StringIO(unicode(xmlStr,'gbk','ignore'))) #(xmlStr,'utf-8','ignore')
        
        return handler.sid,handler.iconId,handler.pkg,handler.versionCode,handler.versionName
        
'''
if __name__ == '__main__':

    parser = xml.sax.make_parser()

    handler = QuotationHandler()

    parser.setContentHandler(handler)

    parser.parse("map")
'''
    

