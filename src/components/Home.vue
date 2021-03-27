<template>
  <div>
    <div style="margin-bottom: 60px">
      基于W2C-CNN-BiGRU的Webshell在线检测系统
    </div>
    <el-upload
      class="upload-demo"
      action=""
      multiple
      :http-request="handleHttpRequest"
      >

<!--      <i class="el-icon-upload"></i>-->
<!--      <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>-->
<!--      <div class="el-upload__tip" slot="tip" style="color: orangered">只能上传PHP脚本文件</div>-->
      <el-button size="small" type="primary">点击上传</el-button>
      <div slot="tip" class="el-upload__tip" style="color:orangered;margin-bottom: 10px">只能上传php文件，不能超过10M</div>
    </el-upload>

    <el-table
      :data="tableData"
      stripe
      border
      style="width: 100%" >

      <el-table-column
        prop="filename"
        label="文件名字">
      </el-table-column>

      <el-table-column
        prop="detectres"
        label="检测结果">
      </el-table-column>

      <el-table-column
        prop="webshell"
        label="webshell概率">
      </el-table-column>


      <el-table-column
        prop="normal"
        label="正常网页脚本概率">
      </el-table-column>

      <el-table-column
        prop="date"
        label="日期"
        :formatter="dateFormat"
      >
      </el-table-column>
      <el-table-column
        prop="type"
        label="标签">
      </el-table-column>
    </el-table>
<!--   版本01 有斑马纹路的 <el-table-->
<!--      :data="tableData"-->
<!--      stripe-->
<!--      style="width: 100%" >-->

<!--      <el-table-column-->
<!--        prop="filename"-->
<!--        label="文件名字">-->
<!--      </el-table-column>-->

<!--      <el-table-column-->
<!--        prop="detectres"-->
<!--        label="检测类别">-->
<!--      </el-table-column>-->

<!--      <el-table-column-->
<!--        prop="probability"-->
<!--        label="分类概率">-->
<!--      </el-table-column>-->

<!--      <el-table-column-->
<!--        prop="date"-->
<!--        label="日期"-->
<!--        :formatter="dateFormat"-->
<!--        >-->
<!--      </el-table-column>-->
<!--      <el-table-column-->
<!--        prop="type"-->
<!--        label="标签">-->
<!--      </el-table-column>-->
<!--    </el-table>-->
  </div>
</template>

<script>
  import moment from 'moment'
  export default {
    name: 'Home',
    data () {
      return {
        msg: 'Webshell Home Page',
        search:'',
        probability: '',
        isWebshell: true,
        tableData: []
      }
    },
    created() {
      this.axios.get(
        '/api/all'
      ).then((res) => {
        console.log(res)
        this.tableData = res.data;
      })
    },
    methods: {
      onSearch() {

      },
      handleHttpRequest(obj) {

        console.log(obj.file)
        let param = new FormData();
        param.append('file', obj.file);
        let config = {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        };
        this.axios.post(
          '/api/upload', param, config
        ).then((res) => {
          console.log(res)
          console.log(res.data.message)
          if (res.data.statusCode == 1) {
            this.openYes(res)
          } else {
            this.openNo(res)
          }

        })

      },
      openYes(o) {
        const h = this.$createElement;

        this.$msgbox({
          title: o.data.phpFileName + '检测结果',
          message: h('p', null, [
            h('span', null, '预测为Webshell概率：' + o.data.webshell),
            h('br', null),
            h('span', null, '预测为正常网页脚本概率：' + o.data.normal),
            h('br', null),
            h('span', null, '检测结果：' + o.data.resName),
          ]),
          //showCancelButton: true,
          confirmButtonText: '确定',
        })
      },
      openNo(o) {
        const h = this.$createElement;

        this.$msgbox({
          title: o.data.phpFileName + '检测结果',
          message: h('p', null, [
            h('span', null, '检测失败：'),
            h('br', null),
            h('span', null, '失败原因：' + o.data.message),
          ]),
          //showCancelButton: true,
          confirmButtonText: '确定',
        })
      },
      dateFormat(row, column) {
        let date = row[column.property];
        if (date == undefined) {
          return '';
        }
        return moment(date).format("YYYY-MM-DD HH:mm:ss")
      }
    }
  }
</script>
