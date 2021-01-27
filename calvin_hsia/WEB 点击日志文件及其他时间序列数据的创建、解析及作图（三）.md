# Creating, parsing, graphing web hit logfiles or other temporal data

作者：Calvin_Hsia

时间：2004年10月28日，星期四，6:30 PM

翻译：xinjie

 
```foxpro
DEFINE CLASS ParseLogs as custom

      cDir="C:\WINDOWS\System32\LogFiles\W3svc1\"

      PROCEDURE init

            CREATE TABLE  webhits (dtime t,ip c(15),method c(20),uri c(50))

            n=ADIR(aFiles,this.cDir+"*.log")

            FOR i = 1 TO n    && 遍历每一个日志文件(日志文件名：W2SVC1\exyymmdd.log)

                  this.ProcFiles(this.cDir+aFiles[i,1])

            ENDFOR

      PROCEDURE ProcFiles(cFile as String)

            ?cFile

            nh=FOPEN(cFile)

            DO WHILE !FEOF(nh)

                  cStr=FGETS(nh)

                  IF ASC(cstr)=0    && 无效行为空记录

                        EXIT

                  ENDIF

                  IF cStr="#" && 如果是日志文件的文件头部分

                        IF cStr="#Date"

                              cDate=SUBSTR(cStr,8)

                              cYr=SUBSTR(cDate,1,4)

                              cMon=SUBSTR(cDate,6,2)

                              cDay=SUBSTR(cDate,9,2)

                              cDate=cMon+"/"+cDay+"/"+cYr

                        ENDIF

                  ELSE

                        nAt=AT(' ',cStr)

                        cTime=LEFT(cStr,nAt-1)

                        cStr=SUBSTR(cStr,nAt+1)

                        dtime=CTOT(cDate+" "+cTime)   && 时间 

                         nAt=AT(' ',cStr)

                        ip=LEFT(cStr,nAt-1)           && IP 地址

                        cStr=SUBSTR(cStr,nAt+1)

                        nAt=AT(' ',cStr)

                        method=LEFT(cStr,nAt-1) && 方法

                        cStr=SUBSTR(cStr,nAt+1)

                        nAt=AT(' ',cStr)

                        uri=LEFT(cStr,nAt-1)    && URI

                        cStr=SUBSTR(cStr,nAt+1)

                        IF !EMPTY(m.dtime)

                              INSERT INTO webhits FROM memvar

                          ENDIF

                  ENDIF

            ENDDO

            FCLOSE(nh)

ENDDEFINE   
```
