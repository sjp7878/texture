---
layout: post
title:  "Flex2.0 Chart Usage Main"
description: Flex2.0 Chart Usage Main
date:   2007-10-19 08:31:44 +0530
categories: Flex
---

Flex2.0 Chart Usage Main

```flex
<?xml version="1.0" encoding="utf-8"?>
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" 
     xmlns:bm="http://www.bluemind.co.kr" layout="vertical" xmlns:comp="*" xmlns:ns1="base.*"
     width="100%" height="100%" initialize="initApp()" horizontalAlign="left" backgroundColor="#FFFFFF">
<mx:Script>
    <![CDATA[
        import mx.utils.ObjectUtil;
        import mx.rpc.events.FaultEvent;
        import mx.rpc.events.ResultEvent;
        import mx.collections.ArrayCollection;
        import mx.controls.Alert;
        import de.richinternet.utils.*;
        import mx.formatters.*;
        import base.blue.grid.DatabaseGrid;// blue on
        import blueone.controls.dataGridClasses.BlueDataGridColumn;
        
        private var thisPageCode:String ="bemon/bscm/bscm10200.swf";
        
        private var setSelOjb:String;
        private var setObjId:String;
        
        [Bindable]
        private var firstSite:Array = [{label:"ALL", data:"all"},{label:"HQ(DP)", data:"OY"},{label:"HQ(외주)", data:"EX"},{label:"SESS", data:"SU"}];//,{label:"ALL", data:"all"}
        [Bindable]
        private var secondSalesCode:Array = [{label:"ALL", data:"all"},{label:"Comp", data:"K"},{label:"Mod", data:"M"}];
        [Bindable]
        private var thirdWeek:Object;    //동적으로 계속 바뀜.
        [Bindable]
        private var category:Array = [{label:"FAMILY1", data:"family1"}, {label:"FAMILY2", data:"family2"}, {label:"FAMILY3", data:"family3"}, 
                                    {label:"PACK_TYPE", data:"pack_type"}, {label:"DIE", data:"die"}, {label:"ORGANIZATION", data:"organization"}, 
                                    {label:"DENSITY", data:"density"}, {label:"SPEED", data:"speed"}, {label:"LINE", data:"line"}, 
                                    {label:"WAFER_BIZ", data:"wafer_biz"}, {label:"MASK_REVISION", data:"mask_revision"}, 
                                    {label:"WAFER_SIZE", data:"wafer_size"}, {label:"PACK_PIN", data:"pack_pin"}, {label:"LEAD_FREE_FLAG", data:"lead_free_flag"}];
        
        [Bindable]
        public var analysis:Object;    //ViewStack Data
        [Bindable]
        public var statusData:Object;    //왼쪽 아래 dataGrid Data
        [Bindable]
        private var getLeftCht:ArrayCollection;
        [Bindable]
        private var getRightCht:ArrayCollection;
        
        private function initApp():void{
            cWinPan.menuCode.text = thisPageCode;
        }
        
        public function initButton():void{
             cWinPan.initApp();
        }

        /** 조회 버튼 클릭 처리   필수**/
        public function actSearch():void{
            ro10200.leftChart(cbsite.selectedItem.data, cbmodule.selectedItem.data);
        }

        /** 등록 버튼 클릭 처리   필수*/
        public function actInsert():void{
        }

        /** 저장 버튼 클릭 처리   필수**/
        public function actSave():void{
        }

        /** 삭제 버튼 클릭 처리   필수**/
        public function actDelete():void{
        }

        /** 엑셀 버튼 클릭 처리   필수**/
        public function actExcel():void{
        }

         /** My selection 클릭 처리 **/
        public function actMsCheck():void{
            //cWinPan.useMyChoice, find_md(라인별, 부서별 조회): Disable
        }
        
         public function dgInitApp(dgKind:String, dgSame:BlueDataGrid, isSub:Boolean):void{    //id:dg10201 DataGrid는 그대로 테이블에서 가져오고, 나머지는 dg10202로 가져옴
             if(dgKind == "1"){    //그대로 테이블에서 가져옴
                initHeader(dgSame, "bscm10200", isSub);
             }else if(dgKind == "2"){    //id:dg10202, dg10202_1, dg10202_2, dg10202_3 모두 컬럼이 같으므로 db에선 하나의 id만 이용하고 뿌려줄텐 다른 아이디로  뿌려줌
                initHeader(dgSame, "bscm10200", isSub);
             }
        }
        
        public function initHeader(objDataGrid:BlueDataGrid, pgCd:String, isSub:Boolean):void{
            var dgHeader:DatabaseGrid = new DatabaseGrid();
            var tmpDataGrid:String = objDataGrid.name.toString();
            if(tmpDataGrid == "dg10202" || tmpDataGrid == "dg10202_1" || tmpDataGrid == "dg10202_2" || tmpDataGrid == "dg10202_3"){//모두 DB에 같은 id를 사용(컬럼이 같으므로)
                setSelOjb= pgCd;
                dgHeader._grid = objDataGrid;
                if(isSub){
                    dgHeader.useRemoteObject(setSelOjb, 'kor', 'dg10202_sub'); // Category 두개 선택했을시
                }else{
                    dgHeader.useRemoteObject(setSelOjb, 'kor', 'dg10202'); // 페이지, 언어, 컴포넌트, Category하나 선택했을시
                }
            }else if(tmpDataGrid == "dg10201"){
                if(isSub){
                    setObjId = 'dg10201_sub';    // Category 두개 선택했을시
                }else{
                    setObjId = objDataGrid.name.toString();
                }
                setSelOjb= pgCd;
                dgHeader._grid = objDataGrid;
                dgHeader.useRemoteObject(setSelOjb, 'kor', setObjId); // 페이지, 언어, 컴포넌트, Category하나 선택했을시
            }
        }
        
        private function getTabData(tabIndex:int):void{
            //var tmpsite:String = cbsite.selectedItem.data==null?"all":cbsite.selectedItem.data;
             //var tmpcbweek:String = cbweek.selectedItem.data==null?thirdWeek[0].data:cbweek.selectedItem.data;
            var rsTabIndex:String;
            var tmpcategory:Array = new Array(); 
            
             if(rsCategoryChk.length == 0){
                 tmpcategory.length = 0;
                 tmpcategory.push(category[0].data);
             }else{
                 tmpcategory.length = 0;
                 tmpcategory = rsCategoryChk;
             }
            //Dumper.dump("tmpcategory length====>"+tmpcategory.length);
             
            if(tabIndex == 0){
                rsTabIndex = "MVP_IN";
            }else if(tabIndex == 1){
                rsTabIndex = "MBT_IN";
            }else if(tabIndex == 2){
                rsTabIndex = "TEST_IN";
            }else if(tabIndex == 3){
                rsTabIndex = "ASSY_OUT";
            }
            ro10200.search(rsTabIndex, cbsite.selectedItem.data, cbmodule.selectedItem.data, cbweek.selectedItem.data, tmpcategory);
        }
        
        private function showTitle(tabIndex:int):void{    //ViewStack에 나타나는 title 
            var rsTabIndex2:String;
            
            displayDataGrid('R', vs.selectedIndex);    //tab change될때마다 이함수를 싷행해서 header를 가져온다
            if(tabIndex == 0){
                rsTabIndex2 = "MVP_IN";
            }else if(tabIndex == 1){
                rsTabIndex2 = "MBT_IN";
            }else if(tabIndex == 2){
                rsTabIndex2 = "TEST_IN";
            }else if(tabIndex == 3){
                rsTabIndex2 = "ASSY_OUT";
            }
            lbObjectTitle.text = "┃ "+rsTabIndex2;
        }
        
        /**
        *    @parameter lr:왼쪽 datagrid인지 오른쪽 datagrid인지, idx:오른쪽 datagrid일경우 현재 선택된 viewStack의 index
        *    lr로 datagrid 왼쪽인지 오른쪽인지, 오른쪽일 경우 idx로, compLen으로 카테고리 개수에 따라 컬럼의 header를 가져온다.
        */    
        private function displayDataGrid(lr:String, idx:Number):void{    
            var compLen:int = rsCategoryChk.length;
            
            switch(lr){
                case "L":
                    if(compLen == 0 || compLen == 1){
                        dgInitApp('1', dg10201, false);
                        break;
                    }else if(compLen == 2){
                        dgInitApp('1', dg10201, true);
                        break;
                    }
                case "R":
                    if(compLen == 0 || compLen == 1){
                        if(idx == 0){
                            dgInitApp('2', dg10202, false);
                            break;
                        }else if(idx == 1){
                            dgInitApp('2', dg10202_1, false);
                            break;
                        }else if(idx == 2){
                            dgInitApp('2', dg10202_2, false);
                            break;
                        }else if(idx == 3){
                            dgInitApp('2', dg10202_3, false);
                            break;
                        }
                    }else if(compLen == 2){
                        if(idx == 0){
                            dgInitApp('2', dg10202, true);
                            break;
                        }else if(idx == 1){
                            dgInitApp('2', dg10202_1, true);
                            break;
                        }else if(idx == 2){
                            dgInitApp('2', dg10202_2, true);
                            break;
                        }else if(idx == 3){
                            dgInitApp('2', dg10202_3, true);
                            break;
                        }
                    }
            }
        }
        
        private function labelNumFormatPointOver(item:Object, column:BlueDataGridColumn):String{    //0.으로 시작하는 숫자 0 안나오는것  방지하는 함수
            var rtnValue:String = "";
            var getNumData:Number= Number(item[column.dataField]);
            var numFormater:NumberFormatter = new NumberFormatter();

            if( getNumData >0 && getNumData < 1 ) {
                rtnValue= '0'+ numFormater.format(getNumData);
            }else{
                rtnValue= numFormater.format(getNumData);
            }
            return rtnValue;
        }
        
        //위쪽 Chart Size 변경
        private function doChartSizeChange(getTarget:String, kindChart:String):void{
            if(kindChart == 'L'){
                if(getTarget == "MID"){
                    left_cht.percentWidth = 100;
                    right_cht.percentWidth = 100;
                    right_cht.visible = true;
                }else if(getTarget == "MAX"){
                    left_cht.percentWidth = 100;
                    right_cht.width = 0;
                    right_cht.visible = false;
                }
            }else if(kindChart == 'R'){
                if(getTarget == "MID"){
                    left_cht.percentWidth = 100;
                    right_cht.percentWidth = 100;
                    left_cht.visible = true;
                }else if(getTarget == "MAX"){
                    right_cht.percentWidth = 100;
                    left_cht.width = 0;
                    left_cht.visible = false;
                }
            }
        }
        
        //아래쪽 DataGrid, ViewStack Size 변경
        private function doSizeChange(getTarget:String, kindChart:String):void{
            if(kindChart == 'L'){
                if(getTarget == "MID"){
                    dg.percentWidth = 100;
                    tb.percentWidth = 100;
                    tb.visible = true;
                }else if(getTarget == "MAX"){
                    dg.percentWidth = 100;
                    tb.width = 0;
                    tb.visible = false;
                }
            }else if(kindChart == 'R'){
                if(getTarget == "MID"){
                    dg.percentWidth = 100;
                    tb.percentWidth = 100;
                    dg.visible = true;
                }else if(getTarget == "MAX"){
                    tb.percentWidth = 100;
                    dg.width = 0;
                    dg.visible = false;
                }
            }
        }
        
        private function doClickCategorySelect():void{
            if(find_index.selected){
                chkIndexSelect.setStyle('borderColor', 0xFF0000);
                vbIndexSelect.visible = true;
                vbIndexSelect.height = 100;
            }else {
                chkIndexSelect.setStyle('borderColor', 0x808080);
                vbIndexSelect.visible = false;
                vbIndexSelect.height = 0;
            }
        }
        
        private function doCloseCategorySelect():void{
            chkIndexSelect.setStyle('borderColor', 0x808080);
            find_index.selected = false;
            doClickCategorySelect();
        }
        
        //숨겨져 있는 VBox에서 CheckBox 선택시 Setting해주는 함수
        [Bindable]
        public var rsCategoryChk:Array = new Array();
        private function chkClick(evt:MouseEvent):void{
            rsCategoryChk.length = 0;
            var cnt:int = 0;
            for(var i:int=0;i<category.length;i++){
                if(cbAssy[i].selected == true){
                    //Alert.show("selected CheckBox==>"+cbAssy[i].label);
                    cnt++;
                    if(cnt > 2){
                        Alert.show("최대 2개까지 밖에 선택할수 없습니다.");
                        evt.target.selected = false;
                    }
                    rsCategoryChk.push(cbAssy[i].data);
                }
            }
        }
        
/**==========================RemoteObject S==========================================    */
        private function getWeekList_result(event:ResultEvent):void{
             thirdWeek = event.result;
             cbweek.dataProvider = thirdWeek;
         }
         
         private function leftChart_result(event:ResultEvent):void{
             getLeftCht = event.result as ArrayCollection;
             bscm10201L.ccTrendChart.dataProvider = getLeftCht;
             
             if(getLeftCht != null)
                 ro10200.rightChart(cbsite.selectedItem.data, cbmodule.selectedItem.data, cbweek.selectedItem.data);
         }
         
         private function rightChart_result(event:ResultEvent):void{
             //getRightCht = ro10200.rightChart.lastResult as ArrayCollection;
             // 이런식으로도 받을 수 있어요. 참고하세요.
             // Debug 하는 방법은 아시죠? 한번 걸어봐주실래요?>
             getRightCht = event.result as ArrayCollection;
             bscm10202R.ccTrendChart.dataProvider = getRightCht;
             
             var vsIndex:Number = vs.selectedIndex;
            var tmpcategory:Array = new Array(); 
            //Dumper.dump("rsCategoryChk====>"+rsCategoryChk);
             if(rsCategoryChk.length == 0){
                 tmpcategory.length = 0;
                 tmpcategory.push(category[0].data);
             }else if(rsCategoryChk.length == 1){
                 tmpcategory.length = 0;
                 tmpcategory = rsCategoryChk;
             }else if(rsCategoryChk.length == 2){
                 tmpcategory.length = 0;
                 tmpcategory = rsCategoryChk;
             }
             //Dumper.dump("tmpcategory Array===>"+tmpcategory);
             displayDataGrid('L', 100);    //왼쪽의 dataGrid의 data를  가져오기전에 header부터 가져옴(7개일때도 있고 8개일때도 있기때문)
             if(getRightCht != null)
                 ro10200.leftDataGrid(cbsite.selectedItem.data, cbmodule.selectedItem.data, cbweek.selectedItem.data, tmpcategory);    //ro10200.leftDataGrid(category[0].data, thirdWeek[0].data);
         }
         
         private function leftDataGrid_result(event:ResultEvent):void{
             statusData = event.result;
             displayDataGrid('R', vs.selectedIndex);    //오른쪽의 dataGrid의 data를  가져오기전에 header부터 가져옴(7개일때도 있고 8개일때도 있기때문)
             if(statusData != null)
                 getTabData(0); 
         }
        
        private function search_result(event:ResultEvent):void{
            if(analysis!= null) analysis.removeAll();
            analysis = event.result;
        }
        
        private function faultHandler(event:FaultEvent):void{
            Alert.show(event.fault.toString(), '오류');
        }
/**==========================RemoteObject E==========================================    */
         
    ]]>
</mx:Script>
        
    <mx:RemoteObject id="ro10200" destination="Bscm10200CD" fault="faultHandler(event)">
       <mx:method name="search" result="search_result(event)"/>
       <mx:method name="leftChart" result="leftChart_result(event)"/>
       <mx:method name="rightChart" result="rightChart_result(event)"/>
       <mx:method name="leftDataGrid" result="leftDataGrid_result(event)"/>
       <mx:method name="getWeekList" result="getWeekList_result(event)"/>
    </mx:RemoteObject>
    
    <!-- 화면 상단의 Title Box 시작 -->
    <mx:HBox width="100%">
        <ns1:cPanWinTop id="cWinPan" creationComplete="initButton()"
            doBtnSearch   ="actSearch()"
            doBtnInsert   ="actInsert()"
            doBtnSave     ="actSave()"
            doBtnDelete   ="actDelete()"
            doBtnExcel    ="actExcel()"
            doMsCheck     ="actMsCheck()"/>
    </mx:HBox>
    <!-- 화면 상단의 Title Box 끝 -->
    
    <mx:HBox width="100%" verticalAlign="middle">
        <mx:ComboBox id="cbsite" dataProvider="{firstSite}" width="110"/>
        <mx:ComboBox id="cbmodule" dataProvider="{secondSalesCode}" width="130"/>
        <mx:ComboBox id="cbweek" width="100" creationComplete="{ro10200.getWeekList()}"/>
        <mx:HBox id="chkIndexSelect" backgroundColor="#E7E7E7" backgroundAlpha=".5" height="22"
            borderStyle="solid" borderColor="#808080" paddingLeft="5" verticalAlign="bottom">
            <mx:CheckBox id="find_index" buttonMode="true"
                label="Category Select" click="doClickCategorySelect()"/>
        </mx:HBox>
    </mx:HBox>
    
   

    <mx:VDividedBox  id="vdbMain" width="100%" height="100%" verticalGap="2">

        <mx:HDividedBox id="hdbTopBox" width="100%" height="100%" horizontalGap="2" horizontalScrollPolicy="off" verticalScrollPolicy="off">

            <mx:VBox id="left_cht" width="100%" height="100%" verticalGap="0"
                borderStyle="solid" borderThickness="2" borderColor="#E6ECF3"
                rollOver="{event.target.setStyle( 'borderColor', '#D1DCE9')}"
                rollOut="{event.target.setStyle( 'borderColor','#E6ECF3')}"
                hideEffect="Fade" showEffect="Fade" horizontalScrollPolicy="off" verticalScrollPolicy="off">

                <mx:HBox width="100%" backgroundColor="#004080" backgroundAlpha=".1" 
                    verticalAlign="middle" horizontalAlign="right" horizontalGap="0">

                    <mx:Label width="100%" text="┃"
                        fontSize="12" fontWeight="bold"
                        paddingTop="0" paddingBottom="0"/>

                    <mx:Legend dataProvider="{bscm10201L.ccTrendChart}"
                        direction="horizontal" markerHeight="10"  horizontalGap="3"
                        paddingBottom="0" paddingTop="0" paddingLeft="0" paddingRight="0"/>


                    <mx:LinkButton
                        overIcon="@Embed('/images/button/mid_over.png')"
                        icon="@Embed('/images/button/mid_on.png')"
                        toolTip="Chart Size Middle" toolTipShow="10" click="doChartSizeChange('MID', 'L')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>
                    <mx:LinkButton
                        overIcon="@Embed('/images/button/max_over.png')"
                        icon="@Embed('/images/button/max_on.png')"
                        toolTip="Chart Size Maximum" toolTipShow="10" click="doChartSizeChange('MAX', 'L')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>

                </mx:HBox>

                <comp:bscm10201 id="bscm10201L" />

            </mx:VBox>
            
            <mx:VBox id="right_cht" width="100%" height="100%" verticalGap="0"
                borderStyle="solid" borderThickness="2" borderColor="#E6ECF3"
                rollOver="{event.target.setStyle( 'borderColor', '#D1DCE9')}"
                rollOut="{event.target.setStyle( 'borderColor','#E6ECF3')}"
                hideEffect="Fade" showEffect="Fade" horizontalScrollPolicy="off" verticalScrollPolicy="off">

                <mx:HBox width="100%" backgroundColor="#004080" backgroundAlpha=".1"
                    verticalAlign="middle" horizontalAlign="right" horizontalGap="0">
                    <mx:Label width="100%" text="┃" fontSize="12" fontWeight="bold" />

                    <mx:Legend dataProvider="{bscm10202R.ccTrendChart}"
                        direction="horizontal" markerHeight="10"  horizontalGap="3"
                        paddingBottom="0" paddingTop="0" paddingLeft="0" paddingRight="0"/>

                    <mx:LinkButton
                        overIcon="@Embed('/images/button/mid_over.png')"
                        icon="@Embed('/images/button/mid_on.png')"
                        toolTip="Chart Size Middle" toolTipShow="10" click="doChartSizeChange('MID', 'R')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>
                    <mx:LinkButton
                        overIcon="@Embed('/images/button/max_over.png')"
                        icon="@Embed('/images/button/max_on.png')"
                        toolTip="Chart Size Maximum" toolTipShow="10" click="doChartSizeChange('MAX', 'R')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>

                </mx:HBox>

                <comp:bscm10202 id="bscm10202R"/>

            </mx:VBox>
            
        </mx:HDividedBox>
        
<!--============================= Hidden VBox S ========================================-->
        <mx:VBox  id="vbIndexSelect" height="0" width="100%"
            hideEffect="Fade" showEffect="Fade" visible="false" verticalGap="1"
            backgroundColor="#FFFFFF" borderColor="#FF8000" borderStyle="solid"
            borderThickness="3" cornerRadius="5"  verticalScrollPolicy="off" horizontalScrollPolicy="off"
            >
    
            <mx:HBox width="100%" paddingLeft="5" backgroundColor="#ff8000" backgroundAlpha=".5">
    
                <mx:Label id="lbIndexTitle" text="※ Category 항목 선택" color="#FF0000" fontSize="12"/>
                <mx:Spacer width="100%"/>
                <mx:Label text="Close" color="#0000FF" useHandCursor="true" buttonMode="true" mouseChildren="false" click="doCloseCategorySelect();"/>
            </mx:HBox>
    
            <mx:VBox id="vbAssyArea" width="100%" height="100%" verticalGap="0" paddingLeft="5"
                backgroundColor="#FF8000" backgroundAlpha=".2" >

                <!--<mx:Label text="Temp" fontSize="12" fontWeight="bold" width="100%"/> -->

                <mx:Tile width="100%" height="100%" verticalScrollPolicy="auto">
                    <mx:Repeater width="100%" height="100%" id="rpAssy" dataProvider="{category}">
                    <!--
                        <mx:CheckBox  width="100%" paddingLeft="0" paddingRight="0" id="cbChoiceStep"
                            tabIndex="{ Number(rpFilterStep.currentItem.index)}"
                            selected="{ rpFilterStep.currentItem.check }"
                            label="{String(rpFilterStep.currentItem.label)}"
                            data="{String(rpFilterStep.currentItem.data)}"
                            click="doSelectedIndexCheckChange(event)"/>
                            -->
                        <mx:CheckBox paddingLeft="0" paddingRight="0" id="cbAssy"  click="chkClick(event)"
                            selected="{rpAssy.currentItem.check}" label="{rpAssy.currentItem.label}" data="{rpAssy.currentItem.data}"
                            />
                    </mx:Repeater>
                </mx:Tile>

            </mx:VBox>
        </mx:VBox>
<!--============================= Hidden VBox E ========================================-->
        <mx:HDividedBox  width="100%" height="100%" horizontalGap="2">

            <mx:VBox id="dg" width="100%" height="100%" verticalGap="0"
                borderStyle="solid" borderThickness="2" borderColor="#E6ECF3"
                rollOver="{event.target.setStyle( 'borderColor', '#D1DCE9')}"
                rollOut="{event.target.setStyle( 'borderColor','#E6ECF3')}"
                hideEffect="Fade" showEffect="Fade">

                <mx:HBox width="100%" backgroundColor="#004080" backgroundAlpha=".1"
                    verticalAlign="middle" horizontalAlign="right" horizontalGap="0">

                    <mx:Label width="100%" text="┃계획 vs 실적 진도현황"
                        fontSize="12" fontWeight="bold" textAlign="left"
                        paddingTop="0" paddingBottom="0"/>
                    <mx:Spacer width="100%"/>
                    <mx:Label width="100%" text="단위:K"
                        fontSize="12" fontWeight="bold" textAlign="right"
                        paddingTop="0" paddingBottom="0"/>

                    <mx:LinkButton
                        overIcon="@Embed('/images/button/mid_over.png')"
                        icon="@Embed('/images/button/mid_on.png')"
                        toolTip="DataGrid Size Middle" toolTipShow="10" click="doSizeChange('MID', 'L')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>
                    <mx:LinkButton
                        overIcon="@Embed('/images/button/max_over.png')"
                        icon="@Embed('/images/button/max_on.png')"
                        toolTip="DataGrid Size Maximum" toolTipShow="10" click="doSizeChange('MAX', 'L')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>

                </mx:HBox>

<!--=========================================== 왼쪽 datagrid S ===========================================-->
                <bm:BlueDataGrid id="dg10201" width="100%" height="100%"
                    dataProvider="{statusData}" creationComplete="{dgInitApp('1', dg10201, false);}" verticalScrollPolicy="auto" />
                <!--id:dg10202, dg10202_1, dg10202_2, dg10202_3 모두 같음
                    headerText:Category, 계획, 실적, 실행율(V), 실행율(I), 계획점유율▼, 차질점유율▼
                    dataField=category, plan-qty, out_qty, volume, item, plan_remain, failure_remain
                -->
<!--=========================================== 왼쪽 datagrid E ===========================================-->

            </mx:VBox>

            <mx:VBox id="tb" width="100%" height="100%" verticalGap="0"
                borderStyle="solid" borderThickness="2" borderColor="#E6ECF3"
                rollOver="{event.target.setStyle( 'borderColor', '#D1DCE9')}"
                rollOut="{event.target.setStyle( 'borderColor','#E6ECF3')}"
                hideEffect="Fade" showEffect="Fade">

                <mx:HBox width="100%" backgroundColor="#004080" backgroundAlpha=".1"
                    verticalAlign="middle" horizontalAlign="right" horizontalGap="0">
                    <mx:Label width="100%" id="lbObjectTitle" text="" fontSize="12" fontWeight="bold"/>
                    
                    <mx:ToggleButtonBar dataProvider="{vs}"/>

                    <mx:LinkButton
                        overIcon="@Embed('/images/button/mid_over.png')"
                        icon="@Embed('/images/button/mid_on.png')"
                        toolTip="TabNavigator Size Middle" toolTipShow="10" click="doSizeChange('MID', 'R')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>
                    <mx:LinkButton
                        overIcon="@Embed('/images/button/max_over.png')"
                        icon="@Embed('/images/button/max_on.png')"
                        toolTip="TabNavigator Size Maximum" toolTipShow="10" click="doSizeChange('MAX', 'R')"
                        rollOverColor="#E1D7FF" selectionColor="#D2C1FF"/>


                </mx:HBox>

<!--======================================= 오른쪽 ViewStack S =========================================-->
                <mx:ViewStack id="vs"  width="100%" height="100%" creationComplete="showTitle(0)" change="getTabData(vs.selectedIndex); showTitle(vs.selectedIndex)">
                    <mx:Canvas label="MVP_IN">
                        <!--id:dg10202, dg10202_1, dg10202_2, dg10202_3 모두 같음
                            headerText:Category, 계획, 실적, 실행율(I), 제공확보(C/O), 당공정차질율(%), 전공정공급차질율(%)
                            dataField=category, plan-qty, out_qty, item, wip_remain, failure1, failure2
                        -->
                        <bm:BlueDataGrid id="dg10202" width="100%" height="100%"
                            dataProvider="{analysis}" creationComplete="{displayDataGrid('R', vs.selectedIndex)}" verticalScrollPolicy="auto" />
                    </mx:Canvas>
                    <mx:Canvas label="MBT_IN">
                        <bm:BlueDataGrid id="dg10202_1" width="100%" height="100%"
                            dataProvider="{analysis}" creationComplete="{displayDataGrid('R', vs.selectedIndex)}" verticalScrollPolicy="auto" />
                    </mx:Canvas>
                    <mx:Canvas label="TEST_IN">
                        <bm:BlueDataGrid id="dg10202_2" width="100%" height="100%"
                            dataProvider="{analysis}" creationComplete="{displayDataGrid('R', vs.selectedIndex)}" verticalScrollPolicy="auto" />
                    </mx:Canvas>
                    <mx:Canvas label="ASSY_OUT">
                        <bm:BlueDataGrid id="dg10202_3" width="100%" height="100%"
                            dataProvider="{analysis}" creationComplete="{displayDataGrid('R', vs.selectedIndex)}" verticalScrollPolicy="auto" />
                    </mx:Canvas>
                </mx:ViewStack>
<!--======================================= 오른쪽 ViewStack E =========================================-->

            </mx:VBox>

        </mx:HDividedBox>
    </mx:VDividedBox>
</mx:Application>
```
