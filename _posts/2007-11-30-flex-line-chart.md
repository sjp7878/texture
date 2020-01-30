---
layout: post
title:  "LabelLineRenderer.as"
description: Flex Chart 관련 LabelLineRenderer.as
date:   2007-11-30 07:34:44 +0530
categories: Flex
---

lex Chart 관련 LabelLineRenderer.as

```flex
package Renderer {

    import flash.display.Graphics;
    import flash.geom.Rectangle;
    import flash.events.*;
    import mx.charts.ChartItem;
    import mx.core.IDataRenderer;
    import mx.graphics.IFill;
    import mx.controls.Label;
    import mx.core.UIComponent;
    import mx.charts.series.items.LineSeriesItem;
    import mx.controls.Alert;
    import mx.charts.LegendItem;
    import mx.charts.series.LineSeries;
    import mx.graphics.IStroke;
    import mx.graphics.Stroke;
    import mx.charts.chartClasses.LegendData;
    import mx.formatters.*;


    public class LabelLineRenderer extends UIComponent implements IDataRenderer
    {
        private var _label:Label;
        [Bindable]
        public var _chartItem:ChartItem;
        public var _legendItem:LegendItem;
        public var _LegendData:LegendData;

        private var fill:Number;
        private var displayAble:Boolean = false;

        private static const fills:Array = [
            0xE48701 , 0xA5BC4E , 0x1B95D9 , 0xCACA9E ,
            0x6693B0 , 0xF05E27 , 0x86D1E4 , 0xE4F9A0 ,
            0xFFD512 , 0x75B000 , 0x0662B0 , 0xEDE8C6 ,
            0xCC3300 , 0xD1DFE7 , 0x52D4CA , 0xC5E05D];

        public function LabelLineRenderer():void {
            super();
            _label = new Label();

            addChild(_label);
            _label.setStyle("color",0x000000);
            _label.setStyle("textAlign","center");

            displayAble = false;
            _label.visible = displayAble;
        }


        public function get data():Object {
            return _chartItem;
        }

        public function set data(value:Object):void {
            var numFormat:NumberFormatter = new NumberFormatter();
            numFormat.thousandsSeparatorTo =',';

            if (_chartItem == value)
                return;
            if(value is LineSeriesItem)
                _chartItem = value as LineSeriesItem;
            else if(value is LegendData)
                _LegendData = value as LegendData;

            if(_chartItem != null){
                _label.text= numFormat.format(Math.round(LineSeriesItem(_chartItem).yNumber*10) /10);
                if(Number(_label.text) == 0 )  _label.visible = false;

            }
            parentDocument.addEventListener(MouseEvent.MOUSE_WHEEL, displayChartValue);

        }

        override protected function updateDisplayList(unscaledWidth:Number,
                                  unscaledHeight:Number):void {
            super.updateDisplayList(unscaledWidth, unscaledHeight);

            var rc:Rectangle = new Rectangle(0, 0, width , height );

            var getWeight:Number = 3;
            var getWGap:Number = 2;
            var getHGap:Number = 2;

            /////////////////////////////////////////////////////we have modify it
            var g:Graphics = graphics;
            g.clear();
            g.moveTo(rc.left,rc.top);
            ///////////////////////////////////////////////////
            // Legend Data Set
            var legendRect:Rectangle = new Rectangle(0, 0, width , height );
            var legendGraph:Graphics = graphics;
            legendGraph.clear();
            if(_LegendData) {
                 legendGraph.beginFill( Stroke(LineSeries(LegendData(_LegendData).element).getStyle("lineStroke")).color ,  Stroke(LineSeries(LegendData(_LegendData).element).getStyle("lineStroke")).alpha);
            }
            legendGraph.drawEllipse(0, 0, width , height );
            legendGraph.endFill();
            ///////////////////////////////////////////////////
            // Chart Data Point Set
            if(_chartItem && LineSeriesItem(_chartItem).element ) {
                 g.beginFill( Stroke(LineSeries(LineSeriesItem(_chartItem).element).getStyle("lineStroke")).color,  Stroke(LineSeries(LineSeriesItem(_chartItem).element).getStyle("lineStroke")).alpha);
                getWeight = Stroke(LineSeries(LineSeriesItem(_chartItem).element).getStyle("lineStroke")).weight;

                getWGap = width - getWeight-1;
                getHGap = height - getWeight-1;
            }
            g.drawEllipse(getWGap/2, getHGap/2, width - getWGap, height - getHGap);
            g.endFill();
            ///////////////////////////////////////////////////
            var labelHeight:Number = _label.getExplicitOrMeasuredHeight();
            var labelWidth:Number = _label.getExplicitOrMeasuredWidth();

            _label.setActualSize(labelWidth, labelHeight);

            if(unscaledHeight < labelHeight )
                 _label.move( (unscaledWidth - labelWidth)/2 ,  labelHeight - 24);


        }
        private function displayChartValue(event:MouseEvent):void{
            if(Number(_label.text) == 0 )  {
                _label.visible = false;
                return;
            }

            if(displayAble  ){
                displayAble = false;
                _label.visible = true;
            } else{
                displayAble = true;
                _label.visible = false;
            }
        }
    }
}

```
