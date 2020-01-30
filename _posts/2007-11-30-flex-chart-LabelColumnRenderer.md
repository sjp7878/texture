---
layout: post
title:  "LabelColumnRenderer.as"
description: Flex Chart 관련 LabelColumnRenderer.as
date:   2007-11-30 07:33:44 +0530
categories: Flex
---

Flex Chart 관련 LabelColumnRenderer.as

```flex
package Renderer {

    import flash.display.Graphics;
    import flash.geom.Rectangle;
    import flash.events.*;
    import mx.charts.ChartItem;
    import mx.core.IDataRenderer;
    import mx.graphics.IFill;
    import mx.controls.Label;
    import mx.graphics.SolidColor;
    import mx.core.UIComponent;
    import mx.charts.series.items.ColumnSeriesItem;
    import mx.controls.Alert;
    import mx.charts.chartClasses.LegendData;
    import mx.charts.series.ColumnSeries;
    import mx.formatters.*;

    public class LabelColumnRenderer extends UIComponent implements IDataRenderer {
        private var _label:Label;
        private var _chartItem:ChartItem;
        private var fill:Number=-1;
        private var displayAble:Boolean = false;
        private var _LegendData: LegendData;

        public function LabelColumnRenderer():void {
            super();
            _label = new Label();
            addChild(_label);
            _label.visible = displayAble;
        }

        public function get data():Object {
            return _chartItem;
        }

        public function set data(value:Object):void {
            var numFormat:NumberFormatter = new NumberFormatter();
            numFormat.thousandsSeparatorTo =',';
            if (_chartItem == value) return;

            if(value is ColumnSeriesItem)
                _chartItem = value as ColumnSeriesItem;
            else if(value is LegendData)
                _LegendData = value as LegendData;

            if(_chartItem != null){
                var yData:Number =  Number(ColumnSeriesItem(_chartItem).yValue) ;
                var mData:Number =  Number(ColumnSeriesItem(_chartItem).minValue) ;

                 var labelValue:Number =  (Math.round((yData - mData)*1000) /1000);
                 _label.text = numFormat.format(labelValue);

                 parentDocument.addEventListener(MouseEvent.MOUSE_WHEEL, displayChartValue);
            }
        }

        override protected function updateDisplayList(unscaledWidth:Number,
                                unscaledHeight:Number):void {
            super.updateDisplayList(unscaledWidth, unscaledHeight);

             var rc:Rectangle = new Rectangle(0, 0, width , height );

            var g:Graphics = graphics;
            g.clear();
            g.moveTo(rc.left,rc.top);

            if(_chartItem && ColumnSeriesItem(_chartItem).element) {
                g.beginFill(SolidColor(ColumnSeries(ColumnSeriesItem(_chartItem).element).getStyle("fill")).color , SolidColor(ColumnSeries(ColumnSeriesItem(_chartItem).element).getStyle("fill")).alpha);
            }else if(_LegendData) {
                g.beginFill(SolidColor(ColumnSeries(LegendData(_LegendData).element).getStyle("fill")).color , SolidColor(ColumnSeries(LegendData(_LegendData).element).getStyle("fill")).alpha);
            }

            g.lineTo(rc.right,rc.top);
            g.lineTo(rc.right,rc.bottom);
            g.lineTo(rc.left,rc.bottom);
            g.lineTo(rc.left,rc.top);
            g.endFill();

            var labelHeight:Number = _label.getExplicitOrMeasuredHeight();
            var labelWidth:Number = _label.getExplicitOrMeasuredWidth();

            _label.setActualSize(labelWidth, labelHeight);

            if(unscaledHeight > labelHeight ){
                _label.move((unscaledWidth - labelWidth)/2,  labelHeight-23);
            }else if(unscaledHeight < labelHeight ){
                  _label.move( (unscaledWidth - labelWidth)/2 ,  labelHeight - 32);
            }else if(unscaledHeight >= labelHeight  && unscaledHeight <= (labelHeight*2)){
                 _label.move( (unscaledWidth - labelWidth)/2 ,  labelHeight - 28);
            }else _label.move( (unscaledWidth - labelWidth)/2 ,  labelHeight - 32);


        }
        private function displayChartValue(event:MouseEvent):void{
            if(displayAble ){ displayAble = false; _label.visible = true;
            } else{ displayAble = true; _label.visible = false;    }
            if(Number(_label.text) == 0) _label.visible = false;
        }
    }
}

```
