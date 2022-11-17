<template>
	<view class="">
		<div class="model">
			<div class="model_name">
				<span>模块名称</span>
				<input type="text" placeholder="请输入模板名称" v-model="value" class="model_input" />
			</div>

			<div class="model_calc">
				<span>计费模式</span>
				<picker @change="bindPickerChange" :value="index" :range="array">
					<!-- <view class="uni-input">{{array[index]}}</view> -->
					<div class="model_click">
						<span>{{ array[index] }}</span>
						<uni-icons type="right"></uni-icons>
					</div>
				</picker>

			</div>
		</div>

		<div class="model">
			<div class="power_sections">
				<span>功率分段</span>
				<div class='new'>
					<uni-icons type="plusempty" @click="add()" size="20px" style="margin-right: 2vw;"></uni-icons>
					<uni-icons type="trash" @click="del()" size="20px"> </uni-icons>
				</div>
			</div>

			<div class='power_content'>
				<ul v-for="(data, index1) in list">
					<li :index1='0'>
						<div class="first">
							<div class="powerbox">
								<input class="uni-input" v-model="data.start" ref="input1" placeholder="功率"
									type="number" @focus="message" @blur="info" />
								<div class="right">W</div>
							</div>
							<div>/</div>
							<div class="powerbox">
								<input class="uni-input" v-model="data.end" @change="changeEnd" ref="input2"
									placeholder="功率" type="number" @focus="message" @blur="info, change()" />
								<div class="right">W</div>
							</div>
						</div>
						<div class="second">
							<div class="powerbox">
								<input class="uni-input" v-model="data.money" placeholder="定价" type="number"
								@focus="message" @blur="info" />
								<div class="right">元</div>
							</div>
							<div>/</div>
							<div class="powerbox">
								<input class="uni-input" v-model="data.hourt" placeholder="时间" type="number"
								@focus="message" @blur="info" />
								<div class="right">小时</div>
							</div>
						</div>
						<div class="mess">
							<div v-show="power1" ref="div1" class="one"></div>
						</div>
					</li>
				</ul>
			</div>
		</div>
		<div class="footer">
			超过<span>500W</span>后按照电度计费，请设置按电度计费的费率，若此处留空，超过<span>500W</span>后将自动断电。浮充功<span>10W</span>，浮充时间<span>1小时</span>。
		</div>
		<button type="submit" class="bt1" @click="formSubmit()">保存</button>

	</view>
</template>

<script>
export default {
	data() {
		return {
			value:'',
			index: 0,
			array: ['实时模式', '固定模式'],
			data: 0,
			list: [{
				start: 0,
				end: 0,
				money: 0,
				hourt: 0
			}],
			index1: 0,
			power1: true,
			power2: true,
			option: {}//接收参数
		}
	},

	onload(option) {
		this.option = {
			type: "edit"
		}
		// if(option.type=='add'){

		// }else{

		// }
	},
	methods: {
		changeEnd() {

		},
		//获取数据
		getINit() {

		},
		// 编进是发生改变
		change(i) {
			// 拿到当前值  赋值给下端end值
			// 且 只有在编进中生效，首次创建有 add 方法控制
			if (this.option.type == 'edit') {
				this.list[i + 1].end = parseInt(this.list[i].end + 1)
			} else {

			}
			//数据刷新model层
			this.$forceUpdate()
		},
		//按费率计算
		bindPickerChange(e) {
			// console.log(e)
			this.index = e.detail.value
		},
		formSubmit: function (e) {
			let sumArr = this.list[this.list.length - 1]
			if (sumArr.start==''||sumArr.end == '' || sumArr.money == '' || sumArr.hourt == ''||this.value=='') {
				uni.showToast({
					title: '保存失败，请填写完毕',
					icon: "none"
				})
			}else{
				uni.showToast({
					title: '已保存',
					icon: "checkmarkempty"
				})
			}
			
		},
		//功率分段-添加删除
		add() {
			//第一步  循环 遍历 拿去每个值 判断是否是空置 如果是空则提示 不可为空先填写 直接return
			//第二步   如果通过直接push 数据
			//创建新方法  失去焦点 功能校验 数据 非空 且小于前者 
			// 数据联动  第二个input 值发生变化 会影响下一个组合中值的第一项发生改变
			let sumArr = this.list[this.list.length - 1]
			
			let data = {
				start: parseInt(this.list[this.list.length - 1].end) + 1,
				end: "",
				money: "",
				hourt: ""
			}
			// console.log("未填写完，不能添加", sumArr);

			if (sumArr.end == '' || sumArr.money == '' || sumArr.hourt == '') {
				// console.log("未填写完，不能添加");
				uni.showToast({
					title: '未填写完，不能添加',
					icon:"none"
				})
				return
			} else {
				this.index1++;
				this.list.push(data)
			}
			// this.list.push(data)	
		},
		del() {
			console.log(this.index1);
			if (this.index1>0) {
				this.list.pop({})
				this.index1--;
			}else{
				uni.showToast({
					title: '不能删除',
					icon:"none"
				})
			}
		},
		//input匹配正则
		message(e){
			this.list.map((x, i) => {
				this.$refs.div1[i].innerHTML = ''
			})
		},
		info(e) {
			var pattern = /^[1-9][0-9]*$/
			// console.log(e.target.value)
			this.list.map((x, i) => {
				console.log(x)
				// 第一步  循环 遍历 拿去每个值 判断是否是空置 如果是空则提示 不可为空先填写 直接return
				if (x.start == '' || x.end == '' || x.money == '' || x.hourt == '') {
					this.$refs.div1[i].innerHTML = '内容不能为空'
				} else if (x.start >= x.end) {
					this.$refs.div1[i].innerHTML = '结束值不能小于开始值'
				} else {
					this.$refs.div1[i].innerHTML = ''
				}
			})
		},
		updata() {

		},
		submit() { }
	},

}
</script>

<style scoped="less">
ul {
	margin: 0;
	padding: 0
}

li {
	list-style: none;
}

.content {
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
}

.model {
	margin-top: 4vh;
	margin-left: 2vw;
	margin-right: 2vw;
	border: 1px solid #eee;
	border-radius: 2vw;
}

.model_name,
.model_calc {
	padding-left: 2vw;
	padding-right: 2vw;
	height: 6vh;
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
	align-content: center;
	font-size: 16px;
}

.model_calc {
	border-top: 1px solid #eee;
}

.model_click {
	color: #007AFF;
}

.model_input {
	width: 35vw;
	text-align: right;
	color: #333;
}

.power_sections {
	padding-left: 2vw;
	line-height: 6vh;
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
}

.power_content {
	padding-left: 4vw;
	padding-right: 4vw;
	margin-bottom: 1vh;
	line-height: 6vh;
}

.new {
	margin-right: 4vw;
}

.mess {
	height: 4vh;
	display: flex;
	flex-wrap: nowrap;
}

.mess .one {
	/* background-color: #090; */
	color: #f00;
	width: 100vw;
}

.first {
	display: flex;
	justify-content: space-between;
	margin-top: 2vh;
}

.second {
	margin-top: 2vh;
	/* margin-bottom: 2vh; */
	display: flex;
	justify-content: space-between;
}

.powerbox {
	display: flex;
	flex-wrap: wrap;
	height: 6vh;
	line-height: 6vh;
	border: 1px solid #ccc;
	border-radius: 2vw;
}

.powerbox input {
	width: 24vw;
	line-height: 6vh;
	height: 6vh;
	padding-left: 2vw;
}

.powerbox .right {
	width: 12vw;
	text-align: center;
	color: #ccc;
	background-color: #eee;
	border-left: 1px solid #ccc;
}

.footer {
	color: #666;
	margin: 2vh 2vw 0vw 2vw;
	padding-left: 4vw;
	padding-right: 4vw;
	border: 1px solid #eee;
	border-radius: 2vw;
	font-size: 12px;
}

.footer span {
	color: #d00;
}

.bt1 {
	margin-left: 4vw;
	margin-right: 4vw;
	margin-top: 30vh;
	background-color: #2979FF;
	color: #fff;
	border-radius: 5vw;
}
</style>
