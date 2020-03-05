<template>
  <div id="form" class="app-container">
    <div class="formCenter">
      <el-form ref="executiveValidateForm" :model="manageArray" status-icon size="small" label-width="136px" class="executiveValidateForm">
        <div v-for="(domain, index) in manageArray.domains" :key="index" class="moreExecutiveIn">
          <div class="title">高管信息(可以填写多个)<img v-if="index !== 0" src="@/assets/404_images/delete.png" alt="" @click.prevent="removeExecutiveDomain(domain)"></div>
          <el-form-item label="客户名称" :prop="'domains.' + index + '.customerName'" :rules="executiveRules.customerName">
            <el-input v-model="domain.customerName" type="text" autocomplete="off" placeholder="请输入客户名称" />
          </el-form-item>
          <el-form-item label="证件类型" :prop="'domains.' + index + '.certType'" :rules="executiveRules.certType">
            <el-select v-model="domain.certType" placeholder="请选择证件类型">
              <el-option v-for="item in fbankCertType" :key="item.code" :label="item.value" :value="item.code" />
            </el-select>
          </el-form-item>
          <div style="position: relative; top: 24px; left: 58px; color: red; margin-top: -16px;">*</div>
          <el-form-item label="证件号码" :prop="'domains.' + index + '.certId'":rules="[{
            validator: (rule, value, callback) => {
              if (!value) {
                return callback('证件号码不能为空')
              }
              var reg = /(^\d{15}$)|(^\d{18}$)|(^\d{17}(\d|X|x)$)/
              if ( domain.certType === '101' && (reg.test(value)) === false) {
                return callback('请正确输入身份证号码')
              }
              callback()
            }, trigger: 'blur'
          }]">
            <el-input v-model="domain.certId" type="text" autocomplete="off" placeholder="请输入证件号码" />
          </el-form-item>
          <el-form-item label="证件登记日期" :prop="'domains.' + index + '.regDate'" :rules="executiveRules.regDate">
            <el-date-picker
              v-model="domain.regDate"
              type="date"
              :clearable="false"
              value-format="yyyy/MM/dd"
              placeholder="证件登记日期"
            />
          </el-form-item>
          <div style="position: relative; top: 24px; left: 29px; color: red; margin-top: -16px;">*</div>
          <el-form-item label="证件到期日期" :prop="'domains.' + index + '.idExpiry'" :rules="[{
            validator: (rule, value, callback) => {
              if (!value) {
                return callback('证件到期日期不能为空')
              }
              if (!domain.regDate) {
                return callback('登记日期不能为空')
              }
              var startTime = new Date(domain.regDate)
              var endTime = new Date(value)
              if (startTime >= endTime) {
                return callback('证件到期日期必须大于证件登记日期')
              }
              callback()
            }, trigger: 'blur'
          }]">
            <el-date-picker
              v-model="domain.idExpiry"
              type="date"
              :clearable="false"
              value-format="yyyy/MM/dd"
              placeholder="证件到期日期"
            />
          </el-form-item>
          <el-form-item label="担任职务" :prop="'domains.' + index + '.relationShip'" :rules="executiveRules.relationShip">
            <el-input v-model="domain.relationShip" type="text" autocomplete="off" placeholder="请输入证件号码" />
          </el-form-item>
        </div>
      </el-form>
    </div>
    <div class="btnSty">
      <el-button type="primary" @click="addOneExecutiveInformationForm">新增高管</el-button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      fbankCertType: [
        { code: '101', value: '身份证' },
        { code: '102', value: '驾驶证' },
        { code: '103', value: '军官证' }
      ],
      // 高管信息
      manageArray: {
        domains: [
          {
            customerName: '',
            certType: '',
            certId: '',
            regDate: '',
            idExpiry: '',
            relationShip: ''
          }
        ]
      },
      // 高管的验证
      executiveRules: {
        customerName: [
          { required: true, message: '客户名称不能为空', trigger: 'blur' },
          { min: 1, max: 50, message: '请正确输入客户名称' }
        ],
        certType: [
          { required: true, message: '证件类型不能为空', trigger: ['blur', 'change'] }
        ],
        certId: [
          { min: 1, max: 32, message: '请正确输入证件编码' }
        ],
        regDate: [
          { required: true, message: '证件登记日期不能为空', trigger: 'blur' }
        ],
        relationShip: [
          { required: true, message: '担任职务不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  methods: {
    // 新增高管按钮
    addOneExecutiveInformationForm() {
      this.manageArray.domains.push({
        customerName: '',
        certType: '',
        certId: '',
        regDate: '',
        idExpiry: '',
        relationShip: ''
      })
    },
    // 删除高管信息
    removeExecutiveDomain(item) {
      this.$confirm('此操作将删除新增内容, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        var index = this.manageArray.domains.indexOf(item)
        if (index !== -1) {
          this.manageArray.domains.splice(index, 1)
        }
        this.$message({
          type: 'success',
          message: '删除成功!'
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消删除'
        })
      })
    }
  }
}
</script>

<style lang="scss" scoped>
#form {
  .formCenter {
    width: 500px;
    margin-bottom: 20px;
    .executiveValidateForm {
      width: 100%;
      .moreExecutiveIn {
        box-shadow: 0px 8px 8px 0px #ccc;
        padding: 20px;
        .title {
          color: pink;
          margin-bottom: 10px;
          img {
            width: 16px;
            height: 16px;
            position: relative;
            top: 4px;
            left: 240px;
          }
        }
      }
    }
  }
}
</style>

