标题：Creating, parsing, graphing web hit logfiles or other temporal data

作者：Calvin_Hsia

时间：2004年10月28日，星期四，6:30 PM

翻译：xinjie

 ```foxpro

CLEAR

CLOSE DATABASES

 

*删除 webhits.dbf && 如果你想解析的就要删除这个文件

IF !FILE("webhits.dbf")

      CREATEOBJECT("ParseLogs")    

ENDIF

 

PUBLIC oGraph as Form

oGraph=NEWOBJECT("GraphForm")

oGraph.Show

 

DEFINE CLASS GraphForm as Form

      allowoutput=.f.

      left=200

      width=1600

      height=300

      backcolor=RGB(255,255,255)

      forecolor=RGB(255,0,255)

      nMin=0

      nMax=0

      nBucketsize=0

      nBuckets=0

      oGridForm=0

      PROCEDURE init

            SELECT MIN(dtime) as min, MAX(dtime) as max FROM webhits INTO CURSOR minmax

            this.nMin=min

            this.nMax=max

            this.nBuckets=this.Width

            this.nBucketSize=(this.nMax-this.nMin) / this.nBuckets

            SELECT INT((dtime-this.nMin)/this.nBucketSize) as bucket,COUNT(*) as cnt FROM webhits GROUP BY 1 INTO CURSOR buckets

            SELECT MAX(cnt) as maxheight FROM buckets INTO CURSOR maxheight

            nMaxHeight=maxHeight.maxHeight

            SELECT buckets

            x0=0

            y0=0

            SCAN  && 遍历游标，并绘制曲线图

                  this.Line(x0,y0,buckets.bucket, this.height-this.height*buckets.cnt/nMaxHeight)

                  x0=buckets.bucket

                  y0= this.height-this.height*buckets.cnt/nMaxHeight

            ENDSCAN

      PROCEDURE Mousedown(nButton, nShift, nXCoord, nYCoord)

            SELECT * FROM webhits WHERE INT((dtime-this.nMin)/this.nBucketSize)=nXCoord INTO CURSOR results

            IF _tally<0

                  SELECT uri,count(*) from results GROUP BY 1 ORDER BY 2 descending INTO CURSOR daily

                  IF VARTYPE(this.oGridForm)!= 'O'

                        this.oGridForm=CREATEOBJECT("form")

                        WITH this.oGridForm

                              .width=500

                              .height=300

                              .visible=1

                              .top=this.Top+this.Height+40

                              .addobject("grid1","grid")

                              WITH .grid1 as Grid

                                    .Visible=1

                                    .Width=thisform.Width

                                    .Height=thisform.Height

                              ENDWITH

                        ENDWITH

                  ENDIF

                  thisform.oGridForm.grid1.recordsource="daily"

                  thisform.oGridForm.grid1.AutoFit

                  thisform.oGridForm.caption = DTOC(TTOD(results.dtime))

            ENDIF

ENDDEFINE
```
