<template>
	<el-dialog title="编辑分类"  :visible.sync="editcationtrue" width="50%" :before-close="colseDialog">
		<el-form :model="form"  ref="ruleformcation" label-width="120px">
			<el-form-item label="分类名称：">
				<el-input v-model="form.cation"></el-input>
			</el-form-item>
			<el-form-item label="可见：">
				<el-switch v-model="form.switch" active-color="#13ce66"></el-switch>
			</el-form-item>
			<el-form-item label="排版地址：">
				<el-input v-model="form.edition"></el-input>
			</el-form-item>
			<el-form-item :label="'区域名称：' + index" v-for="(itemRegion,index) in region" :key="index"     :rules="{
      required: true, message: '区域名称不能为空', trigger: 'blur'
    }">
				<el-input v-model="itemRegion.value" style="width:84%"></el-input>
				<el-button @click.prevent="removeDomain(itemRegion)">删除</el-button>
			</el-form-item>
		</el-form>
		<span slot="footer" class="dialog-footer">
			<el-button @click="addRegion">增加区域</el-button>
			<el-button @click="colseDialogForm('ruleformcation')">取 消</el-button>
			<el-button type="primary" @click="onsubmitmenu('ruleformcation')">保 存</el-button>
		</span>
	</el-dialog>
</template>

<script>
	import Bs from 'common/bus.js';
	import qs from 'qs';
	export default {
		data() {
			return {
				editcationtrue: false,
				form: {
					cation: "",
					switch: true,
					edition: ""
				},
				gomenu: false,
				recapture: true,
				region: [{}]
			}
		},
		props: ['id'],
		mounted() {
			Bs.$on('editcationtrue',(cationdata) =>{
				this.editcationtrue = cationdata;
			})
		},
		methods: {
			colseDialog(done) {
				this.$confirm('确认关闭？ 关闭后什么都不会保存')
				.then(_ => {
					
					done();
					this.fileList = [];
					this.parannum = null;
					//删除上传的文件
					//清开输入框内容
					this.$refs['ruleform'].resetFields();
				})
				.catch(_ => {

				});
			},
			colseDialogForm(formName) {
				let _this = this;
				_this.editcationtrue = false;
				_this.$refs.ruleformcation.resetFields();
			},
			addRegion() {		//添加区域
				this.region.push({
					value: ''
				});

			},
			removeDomain(item) {
				let index=  this.region.indexOf(item);
				if(index != -1){
					this.region.splice(index,1);
				}
			},
			//提交数据
			onsubmitmenu(formName) {
				let _this = this;
				let data = qs.stringify({
							id: _this.id,
							gomenu: _this.gomenu,
							cation: _this.form.cation,
							switch: _this.form.switch,
							edition: _this.form.edition,
							region: _this.region
						});
				_this.$ajax.post('/ai/editcation.php',data).then( (res) => {
							if(res.data == 1){
								_this.$message({
									message: "添加商品成功",
									type: "success"
								})
														// console.log(res)
								_this.$refs.ruleformcation.resetFields();
								_this.editcationtrue = false;
								_this.$emit("recap",_this.recapture);
							}
				})
			}
		},
		watch: {
			"id": function(newval,oloval) {
				if(newval){
					this.$ajax.get("/ai/cation.php",{
						params: {
							gomenu: this.gomenu
						}
					}).then( (res) => {
							console.log(res)
						for(let i = 0; i < res.data.cation.length; i++){
							if(res.data.cation[i].id == newval){
								
								this.form.cation = res.data.cation[i].title;
								this.form.switch = res.data.cation[i].switch;
								this.form.edition = res.data.cation[i].edition;
								this.region = [];
								if(res.data.cation[i].region){
									let regionarr = res.data.cation[i].region.split(',');
									for(let j = 0; j < regionarr.length; j++){
										this.region.push({
											value: regionarr[j]
										});
									}
								}
							}
						}
					})
				}
			}
		}
	}
</script>

<style scoped lang="sass">
	
</style>