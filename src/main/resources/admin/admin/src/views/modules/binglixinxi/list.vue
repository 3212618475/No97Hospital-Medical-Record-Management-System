<template>
	<div class="main-content" :style='{"padding":"0"}'>
		<!-- 列表页 -->
		<template v-if="showFlag">
			<el-form class="center-form-pv" :style='{"width":"92%","margin":"30px 2% 20px 6%"}' :inline="true" :model="searchForm">
				<el-row :style='{"width":"100%"}' >
					<div :style='{"margin":"0 50px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#000","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"600","height":"40px"}' class="item-label">医生工号</label>
						<el-input v-model="searchForm.yishenggonghao" placeholder="医生工号" clearable></el-input>
					</div>
					<div :style='{"margin":"0 50px 0 0","display":"inline-block"}'>
						<label :style='{"margin":"0 10px 0 0","color":"#000","display":"inline-block","lineHeight":"40px","fontSize":"14px","fontWeight":"600","height":"40px"}' class="item-label">科室</label>
						<el-input v-model="searchForm.keshi" placeholder="科室" clearable></el-input>
					</div>
					<el-button :style='{"border":"2px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 4px 0px 0px #B96D62","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"100px","fontSize":"14px","height":"40px"}' type="success" @click="search()">查询</el-button>
				</el-row>

				<el-row :style='{"margin":"30px 0","justifyContent":"flex-end","display":"flex"}'>
					<el-button :style='{"border":"3px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 4px 0px 0px #B96D62","margin":"0 10px 0 0","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"40px"}' v-if="isAuth('binglixinxi','新增')" type="success" @click="addOrUpdateHandler()">新增</el-button>
					<el-button :style='{"border":"3px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 4px 0px 0px #B96D62","margin":"0 10px 0 0","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"40px"}' v-if="isAuth('binglixinxi','删除')" :disabled="dataListSelections.length <= 0" type="danger" @click="deleteHandler()">删除</el-button>


					<el-button :style='{"border":"3px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 4px 0px 0px #B96D62","margin":"0 10px 0 0","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"40px"}' v-if="isAuth('binglixinxi','打印')" type="success" @click="printJson">打印</el-button>


				</el-row>
			</el-form>
			
			<!-- <div> -->
				<el-table class="tables"
					:stripe='false'
					:style='{"padding":"0","borderColor":"rgba(237, 219, 201, 1)","margin":"30px 2% 20px 6%","borderWidth":"1px","background":"#fff","width":"92%","borderStyle":"solid"}' 
					v-if="isAuth('binglixinxi','查看')"
					:data="dataList"
					v-loading="dataListLoading"
				@selection-change="selectionChangeHandler">
					<el-table-column :resizable='true' type="selection" align="center" width="50"></el-table-column>
					<el-table-column :resizable='true' :sortable='false' label="索引" type="index" width="50" />
					<el-table-column :resizable='true' :sortable='false'  
						prop="binglibianhao"
					label="病历编号">
						<template slot-scope="scope">
							{{scope.row.binglibianhao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="bingrenzhanghao"
					label="病人账号">
						<template slot-scope="scope">
							{{scope.row.bingrenzhanghao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="bingrenxingming"
					label="病人姓名">
						<template slot-scope="scope">
							{{scope.row.bingrenxingming}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="yishenggonghao"
					label="医生工号">
						<template slot-scope="scope">
							{{scope.row.yishenggonghao}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="keshi"
					label="科室">
						<template slot-scope="scope">
							{{scope.row.keshi}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="yishengxingming"
					label="医生姓名">
						<template slot-scope="scope">
							{{scope.row.yishengxingming}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="xianbingshi"
					label="现病史">
						<template slot-scope="scope">
							{{scope.row.xianbingshi}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="jiwangshi"
					label="既往史">
						<template slot-scope="scope">
							{{scope.row.jiwangshi}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="yaominshi"
					label="药敏史">
						<template slot-scope="scope">
							{{scope.row.yaominshi}}
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false' prop="bingliwenjian" label="病历文件">
						<template slot-scope="scope">
							<el-button v-if="scope.row.bingliwenjian" type="text" size="small" @click="download($base.url+scope.row.bingliwenjian)">下载</el-button>
                            <span v-else >无</span>
						</template>
					</el-table-column>
					<el-table-column :resizable='true' :sortable='false'  
						prop="dengjishijian"
					label="登记时间">
						<template slot-scope="scope">
							{{scope.row.dengjishijian}}
						</template>
					</el-table-column>
					<el-table-column width="300" label="操作">
						<template slot-scope="scope">
							<el-button :style='{"border":"2px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 2px 0px 0px #B96D62","margin":"0 10px 5px 0","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"32px"}' v-if=" isAuth('binglixinxi','查看')" type="success" size="mini" @click="addOrUpdateHandler(scope.row.id,'info')">详情</el-button>
							<el-button :style='{"border":"2px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 2px 0px 0px #B96D62","margin":"0 10px 5px 0","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"32px"}' v-if=" isAuth('binglixinxi','修改')" type="primary" size="mini" @click="addOrUpdateHandler(scope.row.id)">修改</el-button>





							<el-button :style='{"border":"2px solid rgba(252, 239, 227, 1)","cursor":"pointer","padding":"0 24px","boxShadow":"4px 2px 0px 0px #B96D62","margin":"0 10px 5px 0","outline":"none","color":"#000","borderRadius":"4px","background":"#fff","width":"auto","fontSize":"14px","height":"32px"}' v-if="isAuth('binglixinxi','删除') " type="danger" size="mini" @click="deleteHandler(scope.row.id)">删除</el-button>
						</template>
					</el-table-column>
				</el-table>
				<el-pagination
					@size-change="sizeChangeHandle"
					@current-change="currentChangeHandle"
					:current-page="pageIndex"
					background
					:page-sizes="[10, 20, 30, 50]"
					:page-size="pageSize"
					:layout="layouts.join()"
					:total="totalPage"
					prev-text="<"
					next-text=">"
					:hide-on-single-page="false"
					:style='{"padding":"0","margin":"30px 2% 20px 6%","whiteSpace":"nowrap","color":"#333","textAlign":"center","width":"92%","fontWeight":"500"}'
				></el-pagination>
			<!-- </div> -->
		</template>
		
		<!-- 添加/修改页面  将父组件的search方法传递给子组件-->
		<add-or-update v-if="addOrUpdateFlag" :parent="this" ref="addOrUpdate"></add-or-update>





	</div>
</template>

<script>
import axios from 'axios'
import AddOrUpdate from "./add-or-update";
export default {
  data() {
    return {
      searchForm: {
        key: ""
      },
      form:{},
      dataList: [],
      pageIndex: 1,
      pageSize: 10,
      totalPage: 0,
      dataListLoading: false,
      dataListSelections: [],
      showFlag: true,
      sfshVisiable: false,
      shForm: {},
      chartVisiable: false,
      chartVisiable1: false,
      chartVisiable2: false,
      chartVisiable3: false,
      chartVisiable4: false,
      chartVisiable5: false,
      addOrUpdateFlag:false,
      layouts: ["total","prev","pager","next","sizes","jumper"],

    };
  },
  created() {
    this.init();
    this.getDataList();
    this.contentStyleChange()
  },
  mounted() {
  },
  filters: {
    htmlfilter: function (val) {
      return val.replace(/<[^>]*>/g).replace(/undefined/g,'');
    }
  },
  components: {
    AddOrUpdate,
  },
  methods: {

    contentStyleChange() {
      this.contentPageStyleChange()
    },
    // 分页
    contentPageStyleChange(){
      let arr = []

      // if(this.contents.pageTotal) arr.push('total')
      // if(this.contents.pageSizes) arr.push('sizes')
      // if(this.contents.pagePrevNext){
      //   arr.push('prev')
      //   if(this.contents.pagePager) arr.push('pager')
      //   arr.push('next')
      // }
      // if(this.contents.pageJumper) arr.push('jumper')
      // this.layouts = arr.join()
      // this.contents.pageEachNum = 10
    },








    init () {
    },
    search() {
      this.pageIndex = 1;
      this.getDataList();
    },

    // 获取数据列表
    getDataList() {
      this.dataListLoading = true;
      let params = {
        page: this.pageIndex,
        limit: this.pageSize,
        sort: 'id',
        order: 'desc',
      }
           if(this.searchForm.yishenggonghao!='' && this.searchForm.yishenggonghao!=undefined){
            params['yishenggonghao'] = '%' + this.searchForm.yishenggonghao + '%'
          }
           if(this.searchForm.keshi!='' && this.searchForm.keshi!=undefined){
            params['keshi'] = '%' + this.searchForm.keshi + '%'
          }
      this.$http({
        url: "binglixinxi/page",
        method: "get",
        params: params
      }).then(({ data }) => {
        if (data && data.code === 0) {
          this.dataList = data.data.list;
          this.totalPage = data.data.total;
        } else {
          this.dataList = [];
          this.totalPage = 0;
        }
        this.dataListLoading = false;
      });
    },
    // 每页数
    sizeChangeHandle(val) {
      this.pageSize = val;
      this.pageIndex = 1;
      this.getDataList();
    },
    // 当前页
    currentChangeHandle(val) {
      this.pageIndex = val;
      this.getDataList();
    },
    // 多选
    selectionChangeHandler(val) {
      this.dataListSelections = val;
    },
    // 添加/修改
    addOrUpdateHandler(id,type) {
      this.showFlag = false;
      this.addOrUpdateFlag = true;
      this.crossAddOrUpdateFlag = false;
      if(type!='info'){
        type = 'else';
      }
      this.$nextTick(() => {
        this.$refs.addOrUpdate.init(id,type);
      });
    },
    // 下载
    download(file){
      window.open(`${file}`)
    },
    // 删除
    deleteHandler(id) {
      var ids = id
        ? [Number(id)]
        : this.dataListSelections.map(item => {
            return Number(item.id);
          });
      this.$confirm(`确定进行[${id ? "删除" : "批量删除"}]操作?`, "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      }).then(() => {
        this.$http({
          url: "binglixinxi/delete",
          method: "post",
          data: ids
        }).then(({ data }) => {
          if (data && data.code === 0) {
            this.$message({
              message: "操作成功",
              type: "success",
              duration: 1500,
              onClose: () => {
                this.search();
              }
            });
          } else {
            this.$message.error(data.msg);
          }
        });
      });
    },


    async printJson() {
      //通过getdata调用后台接口获取数据封装到res
      this.list = this.dataList;
      let data = []
      for (let i=0; i < this.list.length; i++) {
          data.push({
          binglibianhao: this.list[i].binglibianhao,
          bingrenzhanghao: this.list[i].bingrenzhanghao,
          bingrenxingming: this.list[i].bingrenxingming,
          yishenggonghao: this.list[i].yishenggonghao,
          keshi: this.list[i].keshi,
          yishengxingming: this.list[i].yishengxingming,
          xianbingshi: this.list[i].xianbingshi,
          jiwangshi: this.list[i].jiwangshi,
          yaominshi: this.list[i].yaominshi,
          dengjishijian: this.list[i].dengjishijian,
        })
      }
      printJS({
        printable: data,
        properties: [
	  {
		field: 'binglibianhao',
		displayName: '病历编号',
		columnSize: 1
	  },
	  {
		field: 'bingrenzhanghao',
		displayName: '病人账号',
		columnSize: 1
	  },
	  {
		field: 'bingrenxingming',
		displayName: '病人姓名',
		columnSize: 1
	  },
	  {
		field: 'yishenggonghao',
		displayName: '医生工号',
		columnSize: 1
	  },
	  {
		field: 'keshi',
		displayName: '科室',
		columnSize: 1
	  },
	  {
		field: 'yishengxingming',
		displayName: '医生姓名',
		columnSize: 1
	  },
	  {
		field: 'xianbingshi',
		displayName: '现病史',
		columnSize: 1
	  },
	  {
		field: 'jiwangshi',
		displayName: '既往史',
		columnSize: 1
	  },
	  {
		field: 'yaominshi',
		displayName: '药敏史',
		columnSize: 1
	  },
	  {
		field: 'dengjishijian',
		displayName: '登记时间',
		columnSize: 1
	  },
        ],
        type: 'json',
        header: '病历信息',
        // 样式设置
        gridStyle: 'border: 2px solid #3971A5;',
        gridHeaderStyle: 'color: red;  border: 2px solid #3971A5;'
      })
    },
  }

};
</script>
<style lang="scss" scoped>
	
	.center-form-pv {
	  .el-date-editor.el-input {
	    width: auto;
	  }
	}
	
	.el-input {
	  width: auto;
	}
	
	// form
	.center-form-pv .el-input /deep/ .el-input__inner {
				border: 2px solid rgba(252, 239, 227, 1);
				border-radius: 4px;
				padding: 0 12px;
				box-shadow: 0px 4px 0px 0px #B96D62;
				outline: none;
				color: #000;
				width: 200px;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .el-select /deep/ .el-input__inner {
				border: 2px solid rgba(252, 239, 227, 1);
				border-radius: 4px;
				padding: 0 10px;
				box-shadow: 0px 4px 0px 0px #B96D62;
				outline: none;
				color: #000;
				width: 170px;
				font-size: 14px;
				height: 40px;
			}
	
	.center-form-pv .el-date-editor /deep/ .el-input__inner {
				border: 2px solid rgba(252, 239, 227, 1);
				border-radius: 4px;
				padding: 0 10px 0 30px;
				box-shadow: 0px 4px 0px 0px #B96D62;
				outline: none;
				color: #000;
				width: 170px;
				font-size: 14px;
				height: 40px;
			}
	
	// table
	.el-table /deep/ .el-table__header-wrapper thead {
				color: #999;
				font-weight: 500;
				width: 100%;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr {
				background: #fff;
			}
	
	.el-table /deep/ .el-table__header-wrapper thead tr th {
				padding: 12px 0;
				border-color: rgba(237, 219, 201, 1);
				border-width: 0 1px 1px 0;
				border-style: solid;
				text-align: center;
			}

	.el-table /deep/ .el-table__header-wrapper thead tr th .cell {
				padding: 0 10px;
				word-wrap: normal;
				word-break: break-all;
				white-space: normal;
				font-weight: bold;
				display: inline-block;
				vertical-align: middle;
				width: 100%;
				line-height: 24px;
				position: relative;
				text-overflow: ellipsis;
			}

	
	.el-table /deep/ .el-table__body-wrapper tbody {
				width: 100%;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr {
				background: #fff;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #999;
				background: #fff;
				border-color: rgba(237, 219, 201, 1);
				border-width: 0 1px 1px 0;
				border-style: solid;
				text-align: center;
			}
	
		
	.el-table /deep/ .el-table__body-wrapper tbody tr:hover td {
				padding: 12px 0;
				color: #000;
				background: rgba(255, 253, 247, 1);
				border-color: rgba(237, 219, 201, 1);
				border-width: 0 1px 1px 0;
				border-style: solid;
				text-align: center;
			}
	
	.el-table /deep/ .el-table__body-wrapper tbody tr td {
				padding: 12px 0;
				color: #999;
				background: #fff;
				border-color: rgba(237, 219, 201, 1);
				border-width: 0 1px 1px 0;
				border-style: solid;
				text-align: center;
			}

	.el-table /deep/ .el-table__body-wrapper tbody tr td .cell {
				padding: 0 10px;
				overflow: hidden;
				word-break: break-all;
				white-space: normal;
				line-height: 24px;
				text-overflow: ellipsis;
			}
	
	// pagination
	.main-content .el-pagination /deep/ .el-pagination__total {
				margin: 0 10px 0 0;
				color: #666;
				font-weight: 400;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: #f4f4f5;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next {
				border: none;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #666;
				background: #f4f4f5;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				min-width: 35px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-prev:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #C0C4CC;
				background: #f4f4f5;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .btn-next:disabled {
				border: none;
				cursor: not-allowed;
				border-radius: 2px;
				padding: 0;
				margin: 0 5px;
				color: #C0C4CC;
				background: #f4f4f5;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}

	.main-content .el-pagination /deep/ .el-pager {
				padding: 0;
				margin: 0;
				display: inline-block;
				vertical-align: top;
			}

	.main-content .el-pagination /deep/ .el-pager .number {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #666;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #f4f4f5;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number:hover {
				cursor: pointer;
				padding: 0 4px;
				margin: 0 5px;
				color: #eddac9;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #f4f4f5;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pager .number.active {
				cursor: default;
				padding: 0 4px;
				margin: 0 5px;
				color: #000;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				border-radius: 2px;
				background: #eddac9;
				text-align: center;
				min-width: 30px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes {
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input {
				margin: 0 5px;
				width: 100px;
				position: relative;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__inner {
				border: 1px solid #DCDFE6;
				cursor: pointer;
				padding: 0 25px 0 8px;
				color: #606266;
				display: inline-block;
				font-size: 13px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: #FFF;
				width: 100%;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input span.el-input__suffix {
				top: 0;
				position: absolute;
				right: 0;
				height: 100%;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__sizes .el-input .el-input__suffix .el-select__caret {
				cursor: pointer;
				color: #C0C4CC;
				width: 25px;
				font-size: 14px;
				line-height: 28px;
				text-align: center;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump {
				margin: 0 0 0 24px;
				color: #606266;
				display: inline-block;
				vertical-align: top;
				font-size: 13px;
				line-height: 28px;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input {
				border-radius: 3px;
				padding: 0 2px;
				margin: 0 2px;
				display: inline-block;
				width: 50px;
				font-size: 14px;
				line-height: 18px;
				position: relative;
				text-align: center;
				height: 28px;
			}
	
	.main-content .el-pagination /deep/ .el-pagination__jump .el-input .el-input__inner {
				border: 1px solid #DCDFE6;
				cursor: pointer;
				padding: 0 3px;
				color: #606266;
				display: inline-block;
				font-size: 14px;
				line-height: 28px;
				border-radius: 3px;
				outline: 0;
				background: #FFF;
				width: 100%;
				text-align: center;
				height: 28px;
			}
</style>
