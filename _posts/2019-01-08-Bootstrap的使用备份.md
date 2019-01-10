---
layout:     post
title:      Bootstrap的使用备份
subtitle:   Bootstrap的使用备份
date:       2019-01-08
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Html
---


> Bootstrap的使用备份

+  示例

```

    
	<!DOCTYPE html>
	<head>
		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
		<!-- Meta, title, CSS, favicons, etc. -->
		<meta charset="utf-8">
		<meta http-equiv="X-UA-Compatible" content="IE=edge">
		<meta name="viewport" content="width=device-width, initial-scale=1">
		<title>终端管理</title>
		
		<script type="text/javascript" src="${rc.contextPath}/static/new/vendors/jquery-autocompleter/jquery-ui.js" ></script>
		<style>
			table thead{
				background-color:#ddd;
			}
			table tr:nth-child(even){ 
				background: #dff0d8; 
			}
			.ui-autocomplete{
				background-color:#fff;
				width:200px;
			}
		</style>   
	  </head>

	  <body class="nav-md">
		<div class="container body">
		  <div class="main_container">
			<!-- page content -->
			<div class="right_col" role="main" style="margin-left: 0px;">
			  <div class="">
				  <div class="row" style="margin-bottom: 20px;">
							<div class="col-md-12 col-sm-12 col-xs-12">
								<div class="panel panel-primary">
									<div class="panel-heading" style="background: #396185;">终端管理</div>
									<div class="panel-body" style="padding: 17px;border-color: #dddddd;">
										<form class="form-inline" role="form">
										  <div class="form-group">
											<label class="control-label">车辆VIN：</label>
											<input type="text" class="form-control input-sm" oninput="vinChange()" id="vehicleVIN" style="width:200px">
										  </div>
										  <div class="form-group" style="margin-top:-5px;">
											<button id="queryButton" type="button" class="btn btn-primary">查询</button>
										  </div>
										</form>
									</div>
								</div>
							</div>
						</div>

				<div class="row">
				  <div class="col-md-12 col-sm-12 col-xs-12">
					<div class="x_panel">
					  <div class="x_content">
					  <table id="terminalInfoTable"></table>
					  </div>
					</div>
				  </div>

				</div>
			  </div>
			</div>
			<!-- /page content -->
		  </div>
		</div>
		
		<!-- Modal -->
			<div class="modal fade" id="myModal" tabindex="-1" role="dialog"
				aria-labelledby="myModalLabel" aria-hidden="true">
				<div class="modal-dialog">
					<form class="form-horizontal form-label-center" id="defaultForm" method="post" novalidate>
						<div class="modal-content">
							<div class="modal-header">
								<button type="button" class="close"
									data-dismiss="modal" aria-label="Close">
									<span aria-hidden="true">&times;</span>
								</button>
								<h4 class="modal-title" id="myModalLabel"></h4>
							</div>
						<div class="modal-body">
							<div class="item form-group">                      
								<label
									class="control-label col-md-5 col-sm-5 col-xs-12"
									for="organizationId">车辆VIN码： 
								</label>
								<div class="col-md-6 col-sm-6 col-xs-12">
									<input type="text" id="mVehicleVIN" name="mVehicleVIN" disabled value="1111111"
										class="form-control col-md-7 col-xs-12" required="required">
								</div>
							</div>
							<div class="item form-group">                      
								<label
									class="control-label col-md-5 col-sm-5 col-xs-12"
									for="organizationId">数据上传频率： <font style="color: red;">*</font>
								</label>
								<div class="col-md-6 col-sm-6 col-xs-12">
									<input type="text" id="mDataUploadFrequency" name="mDataUploadFrequency"
										class="form-control col-md-7 col-xs-12" required="required">
								</div>
							</div>
							<div class="item form-group">                      
								<label
									class="control-label col-md-5 col-sm-5 col-xs-12"
									for="organizationId">位置信息采样周期： <font style="color: red;">*</font>
								</label>
								<div class="col-md-6 col-sm-6 col-xs-12">
									<input type="text" id="mPositionInformationSamplingPeriod" name="mPositionInformationSamplingPeriod"
										class="form-control col-md-7 col-xs-12" required="required">
								</div>
							</div>
							<div class="item form-group">                      
								<label
									class="control-label col-md-5 col-sm-5 col-xs-12"
									for="organizationId">位置信息上报周期： <font style="color: red;">*</font>
								</label>
								<div class="col-md-6 col-sm-6 col-xs-12">
									<input type="text" id="mLocationReportingPeriod" name="mLocationReportingPeriod"
										class="form-control col-md-7 col-xs-12" required="required">
								</div>
							</div>
							<div class="item form-group">                      
								<label
									class="control-label col-md-5 col-sm-5 col-xs-12"
									for="organizationId">车机安全状态上报周期： <font style="color: red;">*</font>
								</label>
								<div class="col-md-6 col-sm-6 col-xs-12">
									<input type="text" id="mCarSafetyReportingPeriod" name="mCarSafetyReportingPeriod"
										class="form-control col-md-7 col-xs-12" required="required">
								</div>
							</div>
						</div>
						<div class="modal-footer">
								<button type="button"
									class="btn btn-default"
									data-dismiss="modal">关闭</button>
								<button type="button"
									class="btn btn-primary" id="save">保存</button>
							</div>
						</div>
					</form>
				</div>
			</div>
		<script type="text/javascript">
		 var $table;
		$(function () {
				toastr.options = {
				 "closeButton": false, //是否显示关闭按钮
				 "debug": false, //是否使用debug模式
				 "positionClass": "toast-top-center",//弹出窗的位置
				 "showDuration": "300",//显示的动画时间
				 "hideDuration": "1000",//消失的动画时间
				 "timeOut": "2000", //展现时间
				 "extendedTimeOut": "1000",//加长展示时间
				 "showEasing": "swing",//显示时的动画缓冲方式
				 "hideEasing": "linear",//消失时的动画缓冲方式
				 "showMethod": "fadeIn",//显示时的动画方式
				 "hideMethod": "fadeOut" //消失时的动画方式
				 };
				$table = $("#terminalInfoTable").bootstrapTable({
					url: '${rc.contextPath}/terminalManage/terminalList',
					method: 'post',
					contentType: "application/x-www-form-urlencoded",
					queryParamsType:'',//查询参数组织方式
					queryParams:queryParams,//请求服务器时所传的参数
					sidePagination:'server',//指定服务器端分页
					search: false,
					searchOnEnterKey: true,
					pagination: false,
					showRefresh: false,
					showToggle: false,
					showColumns: false,
					striped: true,
					sortable: false,
					sortOrder: "asc",
					clickToSelect: false,
					pageNumber:1,
					pageSize: 10,
					pageList: [1, 5, 10, 25, 50, 100],
					iconSize: 'outline',
					toolbar: '#toolbar',
					icons: {
						refresh: 'glyphicon-repeat',
						toggle: 'glyphicon-list-alt',
						columns: 'glyphicon-list'
					},
					responseHandler:function(res){
						//在ajax获取到数据，渲染表格之前，修改数据源
						/*console.log(res)*/
						return res;
					},
					columns: [
						{
							field: 'vehicleVIN',
							title: '车辆VIN',
							align: 'center'
						},
						{
							field: 'dataUploadFrequency',
							title: '数据上传频率',
							align: 'center'
						},
						{
							field: 'positionInformationSamplingPeriod',
							title: '位置信息采样周期',
							align: 'center'
						},
						{
							field: 'locationReportingPeriod',
							title: '位置信息上报周期',
							align: 'center'
						},
						{
							field: 'carSafetyReportingPeriod',
							title: '车机安全状态上报周期',
							align: 'center'
						},
						{
							field: 'vehicleVIN',
							title: '操作',
							align: 'center',
							formatter:function(value,row,index){
								return  "<a href='#' onclick=terminalDetail('"+row.vehicleVIN+"','"+row.dataUploadFrequency+"','"+row.positionInformationSamplingPeriod+"','"+row.locationReportingPeriod+"','"+row.carSafetyReportingPeriod+"')>参数设置</a>";
							}
						}
					]
				});

				//请求服务数据时所传参数
				function queryParams(params){
					return{
						//pageNumber: params.pageNumber,
						//pageSize: params.pageSize,
						vehicleVIN : $("#vehicleVIN").val()
					}
				}
				$("#queryButton").click(function () {
					$table.bootstrapTable('refresh');
				});
			});
			
			
			function terminalDetail(vehicleVIN,dataUploadFrequency,positionInformationSamplingPeriod,locationReportingPeriod,carSafetyReportingPeriod){
				$("#mVehicleVIN").val(vehicleVIN);
				$("#mDataUploadFrequency").val(dataUploadFrequency);
				$("#mPositionInformationSamplingPeriod").val(positionInformationSamplingPeriod);
				$("#mLocationReportingPeriod").val(locationReportingPeriod);
				$("#mCarSafetyReportingPeriod").val(carSafetyReportingPeriod);
				$("#myModalLabel").html("参数查询与设置");
				$("#myModal").modal('show');
			}
			
			
			function vinChange(){
				var  VIN= $("#vehicleVIN").val();
				$.ajax({
					type : "post",  
					url : "${rc.contextPath}/vehicleInfo/fuzzyQuery",  
					data : {
						"vehicleVIN" : VIN,
					},  
					success : function(data){
						var obj = JSON.parse(data);
						var stateCode = obj.universalResponse.stateCode;
						if(stateCode == 0){
							var vmfqs = obj.vehicleMessageFuzzyQuery;
							var VINs = [];
							if(vmfqs != undefined && vmfqs != null){
								for(var i = 0; i < vmfqs.length; i++){
									VINs.push(vmfqs[i].vehicleVIN);
								}
							}
							$("#vehicleVIN").autocomplete({
								source: VINs,
								mustMatch: true,
								showNoSuggestionNotice: true,
								autoFill: true,
								messages: {
									noResults: '',
									results: function() {}
								}
							});
						}
					}  
				});
			}
			
			 $("#save").on("click",function() {
				var vehicleVIN = $("#mVehicleVIN").val();
				var dataUploadFrequency = $("#mDataUploadFrequency").val();
				var positionInformationSamplingPeriod = $("#mPositionInformationSamplingPeriod").val();
				var locationReportingPeriod = $("#mLocationReportingPeriod").val();
				var carSafetyReportingPeriod = $("#mCarSafetyReportingPeriod").val();
				var reg = /^[0-9]*[0-9][0-9]*$/;
				if(vehicleVIN == null || vehicleVIN == undefined || vehicleVIN == ""){
					toastr.info("车辆VIN码不能为空!");
					/*$("#mVehicleVIN").css({
						'outline': 'none',
						'border-color': 'rgba(255, 0, 0, 0.8)'
					});*/
					return;
				}
				if(dataUploadFrequency == null || dataUploadFrequency == undefined || dataUploadFrequency == ""){
					toastr.info("数据上传频率不能为空!");
					return;
				}else{
					var r = /^[0-9]*$/;
					if(!r.test(dataUploadFrequency)){
						toastr.info("数据上传频率只能为数字!");
						return;
					}else if(dataUploadFrequency<0 || dataUploadFrequency>1000000){
						toastr.info("数据上传频率只能在0~1000000!");
						return;
					}
				}     
				if(positionInformationSamplingPeriod == null || positionInformationSamplingPeriod == undefined || positionInformationSamplingPeriod == ""){
					toastr.info("位置信息采样周期不能为空!");
					return;
				} else {
					if(!reg.test(positionInformationSamplingPeriod)){
						toastr.info("位置信息采样周期只能为整数!");
						return;
					}else if(positionInformationSamplingPeriod<0 || positionInformationSamplingPeriod>1000000){
						toastr.info("位置信息采样周期只能在0~1000000!");
						return;
					}
				}  
				if(locationReportingPeriod == null || locationReportingPeriod == undefined || locationReportingPeriod == ""){
					toastr.info("位置信息上报周期不能为空!");
					return;
				} else {
					if(!reg.test(locationReportingPeriod)){
						toastr.info("位置信息上报周期只能为整数!");
						return;
					}else if(locationReportingPeriod<0 || locationReportingPeriod>1000000){
						toastr.info("位置信息上报周期只能在0~1000000!");
						return;
					}
				}
				
				if(carSafetyReportingPeriod == null || carSafetyReportingPeriod == undefined || carSafetyReportingPeriod == ""){
					toastr.info("车机安全状态上报周期不能为空!");
					return;
				} else {
					if(!reg.test(carSafetyReportingPeriod)){
						toastr.info("车机安全状态上报周期只能为整数!");
						return;
					}else if(carSafetyReportingPeriod<0 || carSafetyReportingPeriod>1000000){
						toastr.info("车机安全状态上报周期只能在0~1000000!");
						return;
					}
				}
				var params = {
					"vehicleVIN" : vehicleVIN,
					"dataUploadFrequency" : dataUploadFrequency,
					"positionInformationSamplingPeriod" : positionInformationSamplingPeriod,
					"locationReportingPeriod" : locationReportingPeriod,
					"carSafetyReportingPeriod" : carSafetyReportingPeriod
				};
				$.ajax({
					type : "post",  
					url : "${rc.contextPath}/terminalManage/saveTerminal",  
					data : params, 
					async:false,
					dataType:"json", 
					success : function(data){
						debugger;
						if (data.code == 0) {
							$("#myModal").modal("hide");
							toastr.info("修改配置成功!");
							$table.bootstrapTable('refresh');
						} else {
							$("#myModal").modal("hide");
							toastr.info("修改配置失败！");
						}
					}  
				});
			});
			
		</script>
	  </body>
	</html>



```




    

 



