<template>
    <Tabs value="name1">
        <TabPane label="个人资料" name="name1">
            <Form ref="formValidate" :model="administrator" :label-width="100">
                <FormItem label="账号/手机号：" prop="name" class="myLable">
                    <span class="myLable">{{administrator.phone}}</span>
                    <a class="navbar-togglerk" @click="modalPhone = true">
                        <Icon type="edit" class="myIcon"></Icon>
                    </a>
                    <Modal v-model="modalPhone" title="修改账号/手机号" @on-ok="onChangeAdministratorPhoneNumber()" @on-cancel="cancel">
                        <Form :model="administratorChange" :label-width="80">
                            <Form-item label="新手机号">
                                <Input v-model="administratorChange.PhoneNumber" placeholder="请输入手机号"></Input>
                            </Form-item>
                        </Form>
                    </Modal>
                </FormItem>
                <FormItem label="昵称：" prop="mail" class="myLable">
                    <span class="myLable">{{administrator.name}}</span>
                    <a class="navbar-togglerk" @click="modalName = true">
                        <Icon type="edit" class="myIcon"></Icon>
                    </a>
                    <Modal v-model="modalName" title="修改昵称" @on-ok="onChangeAdministratorName()" @on-cancel="cancel">
                        <Form :model="administratorChange" :label-width="80">
                            <Form-item label="新昵称">
                                <Input v-model="administratorChange.Name" placeholder="请输入昵称"></Input>
                            </Form-item>
                        </Form>
                    </Modal>
                </FormItem>
                <FormItem label="密码：" prop="mail" class="myLable">
                    <span class="myLable">******</span>
                    <a class="navbar-togglerk" @click="modalPassword = true">
                        <Icon type="edit" class="myIcon"></Icon>
                    </a>
                    <Modal v-model="modalPassword" title="修改密码" @on-ok="onChangeAdministratorPassword()" @on-cancel="cancel">
                        <Form :model="administratorChange" :label-width="80">
                            <Form-item label="新密码">
                                <Input v-model="administratorChange.Password" placeholder="请输入新密码"></Input>
                            </Form-item>
                        </Form>
                    </Modal>
                </FormItem>
                <FormItem label="角色：" prop="mail" class="myLable">
                    <span class="myLable">{{administrator.roles}}</span>
                    <a class="navbar-togglerk" @click="modalRoles = true">
                        <Icon type="edit" class="myIcon"></Icon>
                    </a>
                    <Modal v-model="modalRoles" title="修改权限角色" @on-ok="ok" @on-cancel="cancel">
                        <!--
                        <Form :model="formItem" :label-width="80">
                            <Form-item label="当前角色">
                                <Radio-group v-model="formItem.radio">
                                    <Radio label="male">记者</Radio>
                                    <Radio label="female">编辑</Radio>
                                    <Radio label="female">工程师</Radio>
                                </Radio-group>
                            </Form-item>
                        </Form>
                        -->
                        <span>无权修改</span>
                    </Modal>
                </FormItem>
                <FormItem label="状态：" prop="mail" class="myLable">
                    <span class="myLable">已激活</span>
                    <a class="navbar-togglerk" @click="modalStatus = true">
                        <Icon type="edit" class="myIcon"></Icon>
                    </a>
                    <Modal v-model="modalStatus" title="修改权限角色" @on-ok="ok" @on-cancel="cancel">
                        <span>无权修改</span>
                    </Modal>
                </FormItem>
            </Form>
        </TabPane>
    </Tabs>
</template>
<script>
import {
  saveAdministrator,
  getAdministrator,
  updateAdministrator,
  updateAdministratorRole,
  updateAdministratorPassword,
  updateAdministratorStatus,
  updateAdministratorPhoneNumber,
  updateAdministratorName,
  deleteAdministrator,
  listAdministrator
} from "api/administrator";
export default {
  data() {
    let text = '';
    let role = this.$store.getters.roles.toString();
    if(role === 'reporter'){
        text = '记者';
    }else if(role === 'editor'){
        text = '编辑';
    }else if(role === 'engineer'){
        text = '工程师';
    }else{
        text = '未知';
    }
    return {
      administratorChange:{ID:this.$store.getters.uid},
      administrator: {
        roles: text,
        name: this.$store.getters.name,
        phone: this.$store.getters.phone,
        uid: this.$store.getters.uid
      },
      modalPhone: false,
      modalName: false,
      modalPassword: false,
      modalRoles: false,
      modalStatus: false
    };
  },
  methods: {
    ok() {
      this.$Message.info("Clicked ok");
    },
    cancel() {
      this.$Message.info("Clicked cancel");
    },
    onChangeAdministratorPhoneNumber(){
      let phoneNumber = this.administratorChange.PhoneNumber;
      let id = this.administratorChange.ID;
      updateAdministratorPhoneNumber(id,phoneNumber).then(response=>{
          const responseData = response.data;
          const data = responseData.data;
          console.log(data);
          this.administrator.phone = phoneNumber;
          const code = responseData.code;
          const message = responseData.message;
          if(code != 0){
              this.$Message.info(message);
          }else{
              this.$Message.info("修改成功，即将重新登录！");
              let vue = this;
              setTimeout(function() {
                vue.logout();
              }, 2000);
          }
      }).catch(error => {
          console.log(error);
          this.$Message.error(error);
      });
    },
    onChangeAdministratorPassword(){
      let password = this.administratorChange.Password;
      let id = this.administratorChange.ID;
      updateAdministratorPassword(id,password).then(response=>{
          const responseData = response.data;
          const data = responseData.data;
          const code = responseData.code;
          const message = responseData.message;
          if(code != 0){
              this.$Message.info(message);
          }
          console.log(data);
      }).catch(error => {
          console.log(error);
          this.$Message.error(error);
      });
    },
    onChangeAdministratorName(){
      let name = this.administratorChange.Name;
      let id = this.administratorChange.ID;
      updateAdministratorName(id,name).then(response=>{
          const responseData = response.data;
          const data = responseData.data;
          console.log(data);
          this.administrator.name = name;
          const code = responseData.code;
          const message = responseData.message;
          if(code != 0){
              this.$Message.info(message);
          }
      }).catch(error => {
          console.log(error);
          this.$Message.error(error);
      });
    },
    logout() {
        this.$store.dispatch("LogOut").then(() => {
                this.$router.push({ path: "/login" });
            })
            .catch(err => {
                this.$message.error(err);
            });
        }
  }
};
</script>
<style scoped>
.myLable {
  background: lightyellow;
  border-radius: 5px;
}
.myIcon {
  transform: translateY(-50%);
  top: 50%;
  right: 10px;
  position: absolute;
}
</style>