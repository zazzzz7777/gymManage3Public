<template>
  <div>
    <div class="register_view">
      <div class="outTitle_view">
        <div class="outTilte">{{ projectName }}注册</div>
      </div>
      <el-form :model="registerForm" class="register_form">
        <div class="list_item">
          <div class="list_label">用户账号：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.yonghuzhanghao"
            placeholder="请输入用户账号"
            type="text"
          />
        </div>
        <div class="list_item">
          <div class="list_label">用户密码：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.yonghumima"
            placeholder="请输入用户密码"
            type="password"
          />
        </div>
        <div class="list_item">
          <div class="list_label">确认用户密码：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.yonghumima2"
            type="password"
            placeholder="请输入确认用户密码"
          />
        </div>
        <div class="list_item">
          <div class="list_label">用户姓名：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.yonghuxingming"
            placeholder="请输入用户姓名"
            type="text"
          />
        </div>
        <div class="list_item">
          <div class="list_label">头像：</div>
          <div :style="{ width: 'calc(100% - 120px)' }" class="list_file_list">
            <uploads
              action="file/upload"
              tip="请上传头像"
              :limit="3"
              :fileUrls="registerForm.touxiang ? registerForm.touxiang : ''"
              @change="touxiangUploadSuccess"
            >
            </uploads>
          </div>
        </div>
        <div class="list_item">
          <div class="list_label">性别：</div>
          <el-select
            class="list_sel"
            v-model="registerForm.xingbie"
            placeholder="请选择性别"
          >
            <el-option
              v-for="item in yonghuxingbieLists"
              :label="item"
              :value="item"
            ></el-option>
          </el-select>
        </div>

        <div class="list_item">
          <div class="list_label">电话号码：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.dianhuahaoma"
            placeholder="请输入电话号码"
            type="text"
          />
        </div>
        <div class="list_item">
          <div class="list_label">身份证号码：</div>
          <el-input
            class="list_inp"
            v-model="registerForm.shenfenzhenghaoma"
            placeholder="请输入身份证号码"
            type="text"
          />
        </div>
        <div class="list_btn">
          <el-button class="register" type="success" @click="handleRegister"
            >注册</el-button
          >
          <div class="r-login" @click="close">已有账号，直接登录</div>
        </div>
      </el-form>
    </div>
  </div>
</template>
<script setup>
import { ref, getCurrentInstance, nextTick } from 'vue'
const context = getCurrentInstance()?.appContext.config.globalProperties
const projectName = context?.$project.projectName
//获取注册类型
import { useRoute } from 'vue-router'
const route = useRoute()
const tableName = ref('yonghu')

//公共方法
const getUUID = () => {
  return new Date().getTime()
}
const registerForm = ref({
  xingbie: '',
})
const yonghuxingbieLists = ref([])
const init = () => {
  yonghuxingbieLists.value = '男,女'.split(',')
}
const touxiangUploadSuccess = (fileUrls) => {
  registerForm.value.touxiang = fileUrls
}
// 多级联动参数
//注册按钮
const handleRegister = () => {
  let url = tableName.value + '/register'
  if (!registerForm.value.yonghuzhanghao) {
    context?.$toolUtil.message(`用户账号不能为空`, 'error')
    return false
  }
  if (!registerForm.value.yonghumima) {
    context?.$toolUtil.message(`用户密码不能为空`, 'error')
    return false
  }
  if (registerForm.value.yonghumima != registerForm.value.yonghumima2) {
    context?.$toolUtil.message('两次密码输入不一致', 'error')
    return false
  }
  if (!registerForm.value.yonghuxingming) {
    context?.$toolUtil.message(`用户姓名不能为空`, 'error')
    return false
  }
  if (registerForm.value.touxiang != null) {
    registerForm.value.touxiang = registerForm.value.touxiang.replace(
      new RegExp(context?.$config.url, 'g'),
      ''
    )
  }
  if (
    registerForm.value.dianhuahaoma &&
    !context?.$toolUtil.isMobile(registerForm.value.dianhuahaoma)
  ) {
    context?.$toolUtil.message(`电话号码应输入手机格式`, 'error')
    return false
  }
  if (
    registerForm.value.shenfenzhenghaoma &&
    !context?.$toolUtil.checkIdCard(registerForm.value.shenfenzhenghaoma)
  ) {
    context?.$toolUtil.message(`身份证号码应输入身份证格式`, 'error')
    return false
  }

  context
    ?.$http({
      url: url,
      method: 'post',
      data: registerForm.value,
    })
    .then((res) => {
      context?.$toolUtil.message('注册成功', 'success', (obj) => {
        context?.$router.push({
          path: '/login',
        })
      })
    })
}
//返回登录
const close = () => {
  context?.$router.push({
    path: '/login',
  })
}
init()
</script>
<style lang="scss" scoped>
.register_view {
  background-repeat: no-repeat;
  background-size: cover !important;
  background: url(http://localhost:8080/gymManage/file/cb621f6084bf47a280c5ab08f1809c0f.png);
  display: flex;
  min-height: 100vh;
  justify-content: center;
  align-items: center;
  position: relative;
  background-position: center center;
  // 标题盒子
  .outTitle_view {
    margin: -25px 0 0;
    top: 50%;
    left: 0;
    width: 29%;
    line-height: 50px;
    position: fixed;
    text-align: center;
    height: 50px;
    .outTilte {
      color: #5eb6fe;
      font-weight: bold;
      letter-spacing: 6px;
      font-size: 48px;
    }
  }
  // 表单盒子
  .register_form {
    border-radius: 10px;
    padding: 30px 60px;
    margin: 0 0 0 100px;
    background: transparent;
    display: flex;
    width: 600px;
    justify-content: center;
    flex-wrap: wrap;
  }
  // item盒子
  .list_item {
    margin: 10px 0;
    display: flex;
    width: 100%;
    justify-content: flex-start;
    align-items: center;
    // label
    .list_label {
      color: #9e9e9e;
      width: 90px;
      font-size: 14px;
      box-sizing: border-box;
      text-align: center;
    }
    // 输入框
    :deep(.list_inp) {
      border-radius: 20px;
      padding: 0 10px;
      background: #fff;
      width: calc(100% - 90px);
      border-color: #5fb7ff;
      outline-offset: 4px;
      border-width: 2px;
      line-height: 36px;
      box-sizing: border-box;
      border-style: solid;
      height: 36px;
      //去掉默认样式
      .el-input__wrapper {
        border: none;
        box-shadow: none;
        background: none;
        border-radius: 0;
        height: 100%;
        padding: 0;
      }
      .is-focus {
        box-shadow: none !important;
      }
    }
  }
  //下拉框样式
  :deep(.list_sel) {
    border: 2px solid #5fb7ff;
    border-radius: 20px;
    padding: 0 10px;
    background: #fff;
    width: calc(100% - 90px);
    line-height: 36px;
    box-sizing: border-box;
    //去掉默认样式
    .select-trigger {
      height: 100%;
      .el-input {
        height: 100%;
        .el-input__wrapper {
          border: none;
          box-shadow: none;
          background: none;
          border-radius: 0;
          height: 100%;
          padding: 0;
        }
        .is-focus {
          box-shadow: none !important;
        }
      }
    }
  }
  //按钮盒子
  .list_btn {
    margin: 20px 0 0;
    display: flex;
    width: 100%;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    //注册按钮
    .register {
      border: none;
      border-radius: 0;
      color: #fff;
      background: linear-gradient(90deg, #fb843d 0%, #9ec8ea 0%, #5db6ff 100%);
      width: 80%;
      font-size: 20px;
      height: 40px;
    }
    //注册按钮悬浮样式
    .register:hover {
      border: none;
      border-radius: 0;
      background: linear-gradient(270deg, #fb843d 0%, #9ec8ea 0%, #5db6ff 100%);
      width: 80%;
      font-size: 20px;
      height: 40px;
    }
    //已有账号
    .r-login {
      cursor: pointer;
      padding: 10px 0 0;
      color: #999;
      width: 100%;
      font-size: 12px;
      text-align: center;
    }
  }
  //图片上传样式
  .list_file_list {
    //提示语
    :deep(.el-upload__tip) {
      margin: 7px 0 0;
      color: #999;
      display: none;
      font-size: 12px;
      justify-content: flex-start;
      align-items: center;
    }
    //外部盒子
    :deep(.el-upload--picture-card) {
      border: 2px solid #5fb7ff;
      cursor: pointer;
      background-color: #fff;
      border-radius: 20px;
      width: 90px;
      line-height: 100px;
      text-align: center;
      height: 90px;
      //图标
      .el-icon {
        color: #9e9e9e;
        font-size: 32px;
      }
    }
    :deep(.el-upload-list__item) {
      border: 2px solid #5fb7ff;
      cursor: pointer;
      background-color: #fff;
      border-radius: 20px;
      width: 90px;
      line-height: 100px;
      text-align: center;
      height: 90px;
    }
  }
}
</style>
