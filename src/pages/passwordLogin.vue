<template>
	<v-row justify="space-between">	
		<v-col cols="12" md="1"></v-col>
		<v-col cols="12" md="10">
			<v-form ref="form" v-model="valid" lazy-validation>
				<v-text-field v-model="user.name" :counter="11" :rules="nameRules" label="用户名" required></v-text-field>

				<v-text-field v-model="user.password" :rules="passwordRules" label="密码" required></v-text-field>
				<v-row justify="space-between">
					<v-col cols="12" md="4">
						<v-text-field v-model="user.code" :counter="4" :rules="codeRules" label="验证码" required></v-text-field>
					</v-col>
					<v-col cols="12" md="4">
						<img id="code" v-on:click="changeImg" v-bind:src="src" />
					</v-col>
				</v-row>
				<a href="../test1">百分纸牌协议</a>			

				<v-checkbox v-model="checkbox" :rules="[v => !!v || 'You must agree to continue!']" label="Do you agree?" required></v-checkbox>

				<v-btn :disabled="!valid" color="success" class="mr-4" @click="validate">
					登录
				</v-btn>

				<v-btn color="warning" @click="resetValidation">
					去注册
				</v-btn>
			</v-form>
		</v-col>
		<v-col cols="12" md="1"></v-col>
	</v-row>
</template>

<script>
	export default {
		data() {
			return {
				valid: false,
				user: {
					name: 'dddddddd',
					password: 'ssdsddss',
					code: 'dddd',
				},
				message: '',
				nameRules: [
					v => !!v || 'Name is required',
					v => v.length >= 6 || '用户名长度不能小于6',
					v => v.length <= 11 || '用户名长度不能超过11',
					v => /^[a-zA-z0-9]*$/.test(v) || '请输入合法用户名',
				],
				passwordRules: [
					v => !!v || '不能为空',
					v => v.length >= 6 || '密码长度不能小于6'
				],
				codeRules: [
					v => !!v || '不能为空',
					v => v.length == 4 || '',
					// v => /^[0-9a-zA-Z_]{4}$/.test(v) || '请正确输入验证码'
				],
				select: null,
				items: [
					'Item 1',
					'Item 2',
					'Item 3',
					'Item 4',
				],
				checkbox: false,
				src: 'http://localhost:8079/kaptcha?' + new Date()
			}
		},
		methods: {
			validate() {
				if (!this.$refs.form.validate()) {
					return;
				}
				this.$parent.loading = true;
				this.$parent.$refs.schedu.active = true;
				alert(this.$qs.stringify(this.user));
				this.$http({
					method: "post",
					url: "/user/login",
					data: this.$qs.stringify(this.user)
				}).then(s => {
					// alert(s);
					// alert(s.data);
					// alert(s.data.id);
					if(s.data.status != 200){
						this.$parent.loading = false;
						this.$parent.alert = true;
						this.$parent.errorMessage = s.data.msg;
					}else{
						this.$router.push("/register");
					}
				}).catch(() => {
					this.user.password = 'ldferror';
					this.changeImg();
					this.$parent.loading = false;
					// alert('catch');
				});

			},
			resetValidation() {
				this.$parent.loading = false;
				this.$refs.form.reset();

				this.$router.push("/register");
			},
			changeImg() {
				this.src = 'http://localhost:8079/kaptcha?' + new Date()
			},
			remvoeAlert(){
				this.$parent.alert = false;
			}
		},
		watch:{
			/**
			 * 深度监听user数据变化，用户消除错误提示信息
			 */
			user:{
				deep:true, 
				handler:function(){
					this.remvoeAlert();
				}
			}
		}
	}
</script>

<style>
	#code {
		width: 100px;
		height: 50px;
	}
</style>
