---
layout: post
title:  "Flex2.0 Chart Usage"
description: Flex2.0 Chart Usage
date:   2007-10-26 10:19:44 +0530
categories: Flex
---

Flex2.0 Chart Usage with LabelColumnRenderer and LabelLineRenderer

```flex
<?xml version="1.0"?>
<!-- Simple example to demonstrate the GridLines class. -->
<mx:VBox  xmlns:mx="http://www.adobe.com/2006/mxml" xmlns:bm="http://www.bluemind.co.kr"
    xmlns:base="base.hrss.*" xmlns:com="com.sec.controls.*" xmlns:view="*" width="100%" height="100%"
    horizontalScrollPolicy="off" verticalScrollPolicy="off">


    <mx:Script>
        <![CDATA[
        import mx.rpc.events.*;
        import mx.controls.*;
        import mx.events.*;
        import mx.collections.*;
        import Renderer.*;
        import mx.effects.easing.*;
        import mx.charts.HitData;
        import mx.charts.events.ChartItemEvent;
        import mx.charts.series.ColumnSet;
        import mx.charts.series.ColumnSeries;
        //import base.blue.grid.DatabaseGrid;// blue on
        import blueone.controls.dataGridClasses.BlueDataGridColumn;

        import mx.controls.Alert;
        import de.richinternet.utils.*;
        
        [Bindable] private var colsRend:ClassFactory = new ClassFactory( LabelColumnRenderer );
        [Bindable] private var lineRend:ClassFactory = new ClassFactory( LabelLineRenderer );
        
        /*
        [Bindable]
        private var expensesAC:ArrayCollection = parentDocument.;
            
         private function initApp():void{
             ro10200.rightChart();
         }   
     
         private function resultHandler(event:ResultEvent):void{
             expensesAC = event.result as ArrayCollection;
             for(var i:int=0;i<expensesAC.length;i++){
                Dumper.dump("expensesAC1["+i+"]:"+expensesAC[i].work_day);
                Dumper.dump("expensesAC2["+i+"]:"+expensesAC[i].volume);
                Dumper.dump("expensesAC3["+i+"]:"+expensesAC[i].item);
            } 
         }
     
         private function faultHandler(event:FaultEvent):void{
            Alert.show(event.fault.toString(), '오류');
         }
         */
         
         public function setFormatDataTip(hit:HitData):String {
            var chartItem:Object = Object(hit.chartItem);
             var series:Object = Object(hit.element);
             var items:Object = Object(hit.item);

             var getTitleData:String     =  "<b>"+series.displayName+"</b>" ;
            var getProductData:String   = items.item         != null ? "<B>Item :</B>"        +items.item  : "";
             var getStdDivData:String    = items.volume      != null ? "<B>Volume :</B>"        +items.volume     : "";
             
             var getStep:Object = Object( "<B>" + items.week_no + "</b>");

            return getStep+"<br>"+getProductData + "<br>"+getStdDivData;
        }
            
        ]]>
    </mx:Script>

<mx:CartesianChart id="ccTrendChart" width="100%" height="100%" showDataTips="true"
        hideEffect="Fade" showEffect="Fade"
        resizeEffect="Resize" dataTipFunction="setFormatDataTip">

        <mx:horizontalAxis>
<!--            <mx:LinearAxis id="hor_filter"
                minimum="{getHorMin }" maximum="{getHorMax}" baseAtZero="true" interval="1" padding=".5"
                title="Date : Today({get10201Temp.getItemAt(0).work_date}) ~ {get10201Temp.getItemAt(get10201Temp.length-1).work_date}" /> -->
            <mx:CategoryAxis categoryField="week_no"/>
        </mx:horizontalAxis>

        <mx:verticalAxis>
            <mx:LinearAxis baseAtZero="false" title=""/>
        </mx:verticalAxis>

        <mx:horizontalAxisRenderer>
            <mx:AxisRenderer labelGap="0" tickLength="0"/>
        </mx:horizontalAxisRenderer>

        <mx:verticalAxisRenderer>
            <mx:AxisRenderer labelGap="0" tickLength="0"/>
        </mx:verticalAxisRenderer>

        <mx:series>
            <!--<mx:ColumnSet columnWidthRatio=".8" type="clustered"> -->
                <mx:LineSeries xField="week_no" yField="volume" itemRenderer="{lineRend}" displayName="Volume">
                    <mx:lineStroke><mx:Stroke color="#FF0000" weight="2" alpha=".5"/> </mx:lineStroke>
                </mx:LineSeries>

                <mx:LineSeries xField="week_no" yField="item" itemRenderer="{lineRend}" displayName="Item" >
                    <mx:lineStroke><mx:Stroke color="#FF8000" weight="2" alpha=".5"/> </mx:lineStroke>
                </mx:LineSeries>
<!--
                <mx:ColumnSeries xField="work_day" yField="step3_col" itemRenderer="{colsRend}"  >
                    <mx:fill> <mx:SolidColor color="#008000" alpha=".4"/> </mx:fill>
                </mx:ColumnSeries>

                <mx:ColumnSeries xField="work_day" yField="step4_col" itemRenderer="{colsRend}"  >
                    <mx:fill> <mx:SolidColor color="#0000FF" alpha=".4"/> </mx:fill>
                </mx:ColumnSeries>

                <mx:ColumnSeries xField="work_day" yField="step5_col" itemRenderer="{colsRend}" >
                    <mx:fill> <mx:SolidColor color="#400080" alpha=".4"/> </mx:fill>
                </mx:ColumnSeries>
-->
            <!--</mx:ColumnSet> -->
        </mx:series>
<!--
        <mx:secondVerticalAxis>
            <mx:LinearAxis  title="TAT (Hr)" />
        </mx:secondVerticalAxis>

        <mx:secondVerticalAxisRenderer>
            <mx:AxisRenderer placement="right" labelGap="0" tickLength="0"/>
        </mx:secondVerticalAxisRenderer>
        <mx:secondSeries>

            <mx:LineSeries xField="disp_date" yField="step1_lin" itemRenderer="{lineRend}"  >
                <mx:lineStroke><mx:Stroke color="#FF0000" weight="2" alpha=".5"/> </mx:lineStroke>
            </mx:LineSeries>

            <mx:LineSeries xField="disp_date" yField="step2_lin" itemRenderer="{lineRend}" >
                <mx:lineStroke><mx:Stroke color="#FF8000" weight="2" alpha=".5"/> </mx:lineStroke>
            </mx:LineSeries>

            <mx:LineSeries xField="disp_date" yField="step3_lin" itemRenderer="{lineRend}" >
                <mx:lineStroke><mx:Stroke color="#008000" weight="2" alpha=".5"/> </mx:lineStroke>
            </mx:LineSeries>

            <mx:LineSeries xField="disp_date" yField="step4_lin" itemRenderer="{lineRend}">
                <mx:lineStroke><mx:Stroke color="#0000FF" weight="2" alpha=".5"/> </mx:lineStroke>
            </mx:LineSeries>

            <mx:LineSeries xField="disp_date" yField="step5_lin" itemRenderer="{lineRend}" >
                <mx:lineStroke><mx:Stroke color="#400080" weight="2" alpha=".5"/> </mx:lineStroke>
            </mx:LineSeries>

        </mx:secondSeries>
-->

        <mx:annotationElements>
            <mx:Canvas id="cvSelector" width="100%" height="100%"
                horizontalScrollPolicy="off" verticalScrollPolicy="off">
<!--
                <mx:HSlider id="hsRangeSelecter" top="0" left="0" visible="false"
                    width="100%" values="{[getHorMin, getHorMax]}"
                    labels="{getLabels}" minimum="{getHorMin}" maximum="{getHorMax}"
                    liveDragging="true" thumbCount="2"  labelOffset="0"
                    snapInterval="1" change="doRangeSelectChange('H')"/>

                <com:HSlider id="hsRangeSelecter" width="100%" top="0" left="0" visible="false"
                    labelOffset="18" thumbOffset="0" tickOffset="5" tickLength="1" tickThickness="1"
                    trackHighlightSkin="com.sec.skins.SliderHighlightSkin"
                    allowTrackClick="true" allowThumbOverlap="true"
                    showDataTip="true"  dataTipPlacement="bottom"   dataTipOffset="3"
                    showTrackHighlight="true"
                    minimum="{getHorMin}" maximum="{getHorMax}"
                    liveDragging="true" thumbCount="2"
                    values="{[getHorMin,getHorMax]}"
                    snapInterval="1" change="doRangeSelectChange('H')"/>
-->
            </mx:Canvas>
        </mx:annotationElements>

    </mx:CartesianChart>
</mx:VBox>
```
