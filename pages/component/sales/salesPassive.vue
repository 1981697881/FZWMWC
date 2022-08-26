<template>
	<view>
		<cu-custom bgColor="bg-gradual-blue" class="customHead" :isBack="true">
			<block slot="backText">返回</block>
			<block slot="content">销售出库</block>
		</cu-custom>
		<loading :loadModal="loadModal"></loading>
		<uni-fab :pattern="pattern" :horizontal="horizontal" :vertical="vertical" :popMenu="popMenu" distable
			:direction="direction" @fabClick="fabClick"></uni-fab>
		<zhilin-picker v-model="show" :data="chooseList" :title="title" @confirm="chooseClick" />
		<view class="box getheight">
			<view class="cu-bar bg-white solid-bottom" style="height: 60upx;">
				<view class="action">
					单号:
					<text>{{ form.finBillNo }}</text>
				</view>
				<view class="action">
					日期:
					<ruiDatePicker fields="day" class="ruidata" start="2010-00-00" end="2030-12-30" :value="form.fdate"
						@change="bindChange"></ruiDatePicker>
				</view>
				<view class="action">
					数量:
					<text>{{ form.bNum }}</text>
				</view>
			</view>
			<view class="cu-bar bg-white solid-bottom" style="height: 60upx;">
				<view class="action">
					<view style="width: 90px;">部门:</view>
					<ld-select :list="deptList" list-key="FName" value-key="FNumber" placeholder="请选择" clearable
						v-model="form.fdeptID" @change="deptChange"></ld-select>
				</view>
				<view class="action">
					<view style="width: 90px;">仓库:</view>
					<ld-select :list="stockList" disabled list-key="FName" value-key="FNumber" placeholder="请选择"
						clearable v-model="form.fdCStockId" @change="stockChange"></ld-select>
				</view>
			</view>
			
			<view class="cu-bar bg-white solid-bottom" style="height: 60upx;">
				<view class="action">
					<view class="title">客户:{{ form.FCustName }}</view>
					<!-- <ld-select :list="customerList"
				list-key="FName" value-key="FNumber"
				placeholder="请选择"
				clearable
				
				:value="form.FCustNumber"
				@change="customerChange"></ld-select> -->
				</view>
				<button class="cu-btn round lines-blue line-blue shadow" @tap="showModal" :disabled="isDis"
					data-target="Modal">选择</button>
			</view>
			<view class="cu-bar bg-white solid-bottom" style="height: 60upx;">
				<view class="action">
					<view class="title">成本核算对象:</view>
					<input name="input" style="font-size: 13px;text-align: left;" v-model="form.FEntrySelf" />
				</view>
				<button class="cu-btn round lines-blue line-blue shadow" @tap="showModal4"
					data-target="Modal">扫码</button>
			</view>
			<view class="cu-bar bg-white solid-bottom" style="height: 60upx;">
				<view class="action">
					<view class="title">备注:</view>
					<input name="input" style="font-size: 13px;text-align: left;" v-model="form.fnote" />
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName4 == 'Modal' ? 'show' : ''">
			<view class="cu-dialog" style="height: 350upx;">
				<view class="cu-bar bg-white justify-end" style="height: 60upx;">
					<view class="content">扫描</view>
					<view class="action" @tap="hideModal4"><text class="cuIcon-close text-red"></text></view>
				</view>
				<view class="padding-sm">
					<view class="cu-item">
						<view class="content">
							<text class="text-grey">{{form.FEntrySelf}}</text>
						</view>
						<view class="action"><text class="text-grey"></text></view>
					</view>
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName == 'Modal' ? 'show' : ''">
			<view class="cu-dialog" style="height: 70%;margin-top: 20%;">
				<view class="cu-bar bg-white justify-end" style="height: 60upx;">
					<view class="content">客户信息</view>
					<view class="action" @tap="hideModal"><text class="cuIcon-close text-red"></text></view>
				</view>
				<view style="width:100%;height: 100%;overflow: auto;text-align: left;">
					<city-select @cityClick="cityClick" :formatName="formatName" :obtainCitys="customerList"
						:isSearch="true" style="width: auto !important;" ref="citys"></city-select>
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName2 == 'Modal' ? 'show' : ''">
			<view class="cu-dialog" style="height: 350upx;">
				<view class="cu-bar bg-white justify-end" style="height: 60upx;">
					<view class="content">{{ popupForm.headName }}</view>
					<view class="action" @tap="hideModal2"><text class="cuIcon-close text-red"></text></view>
				</view>
				<view>
					<view class="cu-item" style="width: 100%;">
						<view class="flex">
							<view class="flex-sub">
								<view class="cu-form-group">
									<view class="title">批号:</view>
									<input name="input" style="border-bottom: 1px solid;"
										v-model="popupForm.fbatchNo" />
								</view>
							</view>
							<view class="flex-sub">
								<view class="cu-form-group">
									<view class="title">数量:</view>
									<input name="input" type="digit" style="border-bottom: 1px solid;"
										v-model="popupForm.quantity" />
								</view>
							</view>
						</view>
					</view>
					<view class="cu-item" style="width: 100%;">
						<view class="flex">
							<view class="flex-sub">
								<view class="cu-form-group">
									<view class="title">库位:</view>
									<input name="input" style="border-bottom: 1px solid;"
										v-model="popupForm.positions" />
									<button class="cu-btn round lines-red line-red shadow"
										@tap="$manyCk(scanPosition)">扫码</button>
								</view>
							</view>
						</view>
					</view>
				</view>
				<view style="clear: both;" class="cu-bar bg-white justify-end padding-bottom-xl">
					<view class="action">
						<button class="cu-btn line-green text-green" @tap="hideModal2">取消</button>
						<button class="cu-btn bg-green margin-left" @tap="$manyCk(saveCom)">确定</button>
					</view>
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName3 == 'Modal' ? 'show' : ''">
			<view class="cu-dialog" style="height: auto;">
				<view class="cu-bar bg-white justify-end" style="height: 60upx;">
					<view class="content">{{ popupForm.headName }}</view>
					<view class="action" @tap="hideModal2"><text class="cuIcon-close text-red"></text></view>
				</view>
				<view>
					<view class="cu-item" style="width: 100%;">
						<view class="flex">
							<view class="flex-sub">
								<view class="cu-form-group">
									<view class="title">数量:</view>
									<input name="input" type="digit" style="border-bottom: 1px solid;"
										v-model="popupForm.quantity" />
								</view>
							</view>
						</view>
					</view>
				</view>
				<view style="clear: both;" class="cu-bar bg-white justify-end padding-bottom-xl">
					<view class="action">
						<button class="cu-btn line-green text-green" @tap="hideModal3">取消</button>
						<button class="cu-btn bg-green margin-left" @tap="$manyCk(saveCom2)">确定</button>
					</view>
				</view>
			</view>
		</view>
		<scroll-view scroll-y class="page" :style="{ height: pageHeight + 'px' }">
			<view v-for="(item, index) in cuIList" :key="index">
				<view class="cu-list menu-avatar">
					<view class="cu-item" style="width: 100%;margin-top: 2px;height: 260upx;"
						:class="modalName == 'move-box-' + index ? 'move-cur' : ''" @touchstart="ListTouchStart"
						@touchmove="ListTouchMove" @touchend="ListTouchEnd" :data-target="'move-box-' + index">
						<view style="clear: both;width: 100%;">
							<view style="clear: both;width: 100%;" class="grid text-center col-2" data-target="Modal"
								data-number="item.number" @tap="showModal3(index, item)">
								<!-- @tap="showModal2(index, item)" -->
								<view class="text-grey">序号:{{ (item.index = index + 1) }}</view>
								<view class="text-grey">编码:{{ item.number }}</view>
								<view class="text-grey">名称:{{ item.name }}</view>
								<view class="text-grey">数量:{{ item.quantity }}</view>
								<view class="text-grey">批号:{{ item.fbatchNo }}</view>
								<view class="text-grey">单位:{{ item.unitName }}</view>
								<view class="text-grey">规格:{{ item.model }}</view>
								<view class="text-grey">仓位:{{ item.positions }}</view>
								<view class="text-grey">{{ item.stockName }}</view>
							</view>
							<!-- <view class="text-grey text-center">
								<picker @change="PickerChange($event, item)" :value="pickerVal" :range-key="'FName'"
										:range="stockList">
									<view class="picker">
										<button class="cu-btn sm round bg-green shadow">
											<text class="cuIcon-homefill"></text>
											仓库
										</button>
									</view>
								</picker>
							</view> -->
						</view>
						<view class="move">
							<view class="bg-red" @tap="del(index, item)">删除</view>
						</view>
					</view>
				</view>
			</view>
			<!-- <view class="selectTrees">
				<view class="lv1list" v-for="(item, index) in cuIList" :key="index"
					@longpress="deleteItem(index, item)">
					<view class="tree-one" style="background: white;width: 100%;margin-top: 2px;height: 150upx;">
						<checkbox-group v-if="showCheck"
							style="position: absolute;height: 80rpx;line-height: 150upx; left:20rpx;z-index: 1;">
							<checkbox :checked="item.checked" @click="_chooseAll(item, index)" />
						</checkbox-group>
						<label
							style="height:100%;display: flex;align-items: center;padding: 20rpx;position: relative;border-bottom: 1px solid #e4e4e4;"
							@click="_showlv2(index)">
							<view style="clear: both;width: 100%;" class="grid text-center col-2">
								<view class="itemT">编码:{{ item.number }}</view>
								<view class="itemT">名称:{{ item.name }}</view>
								<view class="itemT">单位:{{ item.unitName }}</view>
								<view class="itemT">规格:{{ item.model }}</view>
								<view class="itemT">应发数量:{{ item.Fauxqty }}</view>
								<view class="itemT">实际数量:{{ item.FCounty }}</view>
							</view>
							<i class="cuIcon-unfold" v-if="item.show"
								style="position: absolute;top: 40%;right: 2%;font-size: 48rpx;"></i>
							<i class="cuIcon-fold" v-else
								style="position: absolute;top: 40%;right: 2%;font-size: 48rpx;"></i>
						</label>
					</view>
					<view v-if="item.show && item.childrenList">
						<view class="tree-two" v-for="(item2, index2) in item.childrenList" :key="index2"
							style="display: flex;">
							<view class="aui-list-item-inner flexIn">
								<checkbox-group v-if="showCheck">
									<checkbox v-if="!disableLv2Check" :checked="item2.checked"
										@click="_chooseOne(index, index2)" />
									<checkbox :checked="item2.checked" disabled="true" v-else />
								</checkbox-group>
								<view style="clear: both;width: 100%;" class="grid text-center col-2">
									<view class="itemO">批号:{{ item2.FBatchNo }}</view>
									<view class="itemO">仓库:{{ item2.FStockName }}</view>
									<view class="itemO">仓位:{{ item2.FStockPlacename }}</view>
									<view class="itemO">库存数:{{ item2.FQty }}</view>
									<view class="itemO" style="width: 80% !important;">
										<view class="title" style="float: left;margin-left: 25%;">出库数:{{item2.quantity}}<text class="sm text-gray cuIcon-edit" @tap="showModal3(index, item2)"></text></view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view> -->
			<view class="cu-bar tabbar shadow foot">
				<view class="box text-center">
					<button :disabled="isClick" class="cu-btn bg-green shadow-blur round lg"
						style="width: 40%;margin-right: 10%;" @tap="$manyCk(saveData)">提交</button>
					<button class="cu-btn bg-blue shadow-blur round lg" style="width: 40%;"
						@tap="$manyCk(clearList)">清空</button>
				</view>
			</view>
		</scroll-view>
	</view>
</template>
<script>
	import ruiDatePicker from '@/components/rattenking-dtpicker/rattenking-dtpicker.vue';
	import ldSelect from '@/components/ld-select/ld-select.vue';
	import uniFab from '@/components/uni-fab/uni-fab.vue';
	import basic from '@/api/basic';
	import sales from '@/api/sales';
	import citySelect from '@/components/city-select/city-select.vue';
	import loading from '@/components/loading';
	import service from '@/service.js';
	import zhilinPicker from '@/components/zhilin-picker/zhilin-picker.vue';
	import selectTree from '@/components/select-tree/select-tree';
	export default {
		components: {
			selectTree,
			zhilinPicker,
			ruiDatePicker,
			ldSelect,
			uniFab,
			loading,
			citySelect
		},
		props: {
			showCheck: {
				//显示多选框
				type: Boolean,
				default: true
			},
			disableLv2Check: {
				//让二级标题不可选中
				type: Boolean,
				default: false
			},
			showDelete: {
				//显示删除按钮
				type: Boolean,
				default: true
			}
		},
		data() {
			return {
				finalList: [],
				menuKey: 1,
				cuIList: [],
				show: false,
				title: '选择库存',
				formatName: 'FName',
				pageHeight: 0,
				headName: '',
				isOrder: false,
				isDis: false,
				loadModal: false,
				onoff: true,
				isClick: false,
				isScanOf: false,
				pickerVal: null,
				modalName: null,
				modalName2: null,
				modalName3: null,
				modalName4: null,
				gridCol: 3,
				form: {
					finBillNo: null,
					fdate: '',
					bNum: 0,
					fnote: '',
					FEntrySelf: '',
					FCustNumber: '',
					fbillerID: null,
					fdCStockId: '',
					FCustName: '',
					fdeptID: ''
				},
				borrowItem: {},
				chooseList: [],
				popupForm: {
					fbatchNo: '',
					positions: '',
					quantity: ''
				},
				skin: false,
				listTouchStart: 0,
				listTouchDirection: null,
				deptList: [],
				stockList: [],
				customerList: [],
				horizontal: 'right',
				vertical: 'bottom',
				popMenu: false,
				direction: 'horizontal',
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
					buttonColor: '#007AFF'
				},
				startDate: null,
				endDate: null,
				billNo: null
			};
		},
		watch: {
			cuIList: {
				handler(newValue, oldValue) {
					let number = 0
					let list = this.cuIList
					this.cuIList.forEach((list, index) => {
						console.log(list.childrenList)
						if (typeof(list.childrenList) != "undefined") {
							list.childrenList.forEach((item, index) => {
								if (item.checked) {
									number += Number(item.quantity)
								}
							})
						}

					})
					this.form.bNum = number
				},
				deep: true
			}
		},
		onUnload() {
			// 移除监听事件
			uni.$off('scancodedate');
			this.isScanOf = true;
		},
		onLoad: function(option) {
			let me = this;
			uni.$on('scancodedate', function(data) {
				me.isScanOf = true;
				// _this 这里面的方法用这个 _this.code(data.code)
				if (!me.isOrder) {
					if (me.modalName4 == null) {
						me.getScanInfo(data.code);
					} else {
						me.scanCostObject(data.code);
					}
				}
			});
			if (JSON.stringify(option) != '{}') {
				this.isOrder = true;
				this.isDis = true;
				me.form.fdeptID = option.FDeptNumber;
				me.form.FCustNumber = option.FCustNumber;
				me.form.FCustName = option.FCustName;
				me.startDate = option.startDate;
				me.endDate = option.endDate;
				me.billNo = option.billNo;
				me.source = option.tranType;
				basic
					.getOrderList({
						billNo: option.billNo,
						/* startDate: option.startDate,
						 	 endDate: option.endDate, */
						tranType: option.tranType,
						type: option.type
					})
					.then(res => {
						if (res.success) {
							let data = res.data.list;
							for (let i in data) {
								me.cuIList.push({
									Fdate: data[i].Fdate,
									number: data[i].FItemNumber,
									name: data[i].FItemName,
									model: data[i].FModel,
									FItemID: data[i].FItemID,
									checked: false,
									show: false,
									isLoading: false,
									FCounty: 0,
									childrenList: [],
									FBatchManager: data[i].FBatchManager,
									fsourceBillNo: data[i].FBillNo,
									Famount: data[i].Famount,
									Fauxprice: data[i].Fauxprice,
									fsourceEntryID: data[i].FEntryID,
									fsourceTranType: data[i].FTranType,
									quantity: data[i].Fauxqty,
									Fauxqty: data[i].Fauxqty,
									unitID: data[i].FUnitNumber,
									unitName: data[i].FUnitName
								});
							}
							me.form.bNum = data.length;
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
			}
		},
		onReady: function() {
			var me = this;
			me.loadModal = true;
			me.initMain();
			if (service.getUsers().length > 0) {
				if (service.getUsers()[0].account != '' && service.getUsers()[0].account != 'undefined') {
					me.form.fbillerID = service.getUsers()[0].userId;
					me.form.username = service.getUsers()[0].username;
					uni.getSystemInfo({
						success: function(res) {
							// res - 各种参数
							let info = uni.createSelectorQuery().select('.getheight');
							let customHead = uni.createSelectorQuery().select('.customHead');
							var infoHeight = 0;
							var headHeight = 0;
							info.boundingClientRect(function(data) {
								//data - 各种参数
								infoHeight = data.height;
							}).exec();
							customHead
								.boundingClientRect(function(data) {
									//data - 各种参数
									headHeight = data.height;
								})
								.exec();
							setTimeout(function() {
								me.pageHeight = res.windowHeight - infoHeight - headHeight;
							}, 1000);
						}
					});
				}
			}
		},
		methods: {
			scanCostObject(res) {
				let me = this;
				me.form.FEntrySelf = res;
				me.$set(me.form,'FEntrySelf',res)
				me.modalName4 = null;
			},
			setQuty(val, index) {
				let list = this.cuIList[index].childrenList;
				let count = 0;
				list.forEach((item, index) => {
					if (item.quantity <= item.FQty) {
						if (item.quantity != '' && item.quantity != null) {
							count += Number(item.quantity);
						}
					} else {
						item.quantity = 0;
						return uni.showToast({
							icon: 'none',
							title: '输入数量不能大于库存数量'
						});
					}
				});
				this.cuIList[index].FCounty = count;
			},
			_showlv2(index) {
				if (this.cuIList[index].isLoading) {
					//展开二级目录
					if (this.cuIList[index].show) {
						this.$set(this.cuIList[index], 'show', false);
					} else {
						this.$set(this.cuIList[index], 'show', true);
					}
				} else {
					basic
						.inventoryByBarcode({
							uuid: this.cuIList[index].number + "," + this.cuIList[index].FBatchNo
						})
						/* .selectInvListByItemNumber({ itemNumber: this.cuIList[index].number }) */
						.then(reso => {
							console.log(reso)
							if (reso.success) {
								let data = reso.data;
								data.forEach((item, index) => {
									item.quantity = 0;
									item.checked = false;
								});
								this.cuIList[index].isLoading = true;
								this.cuIList[index].childrenList = data;
								//展开二级目录
								if (this.cuIList[index].show) {
									this.$set(this.cuIList[index], 'show', false);
								} else {
									this.$set(this.cuIList[index], 'show', true);
								}
								this.$forceUpdate();
							}
						})
						.catch(err => {
							uni.showToast({
								icon: 'none',
								title: err.msg
							});
						});
				}
			},
			_chooseAll(item, index) {
				if (this.cuIList[index].isLoading) {
					//选中一级目录的所有 FCounty
					let count = 0;
					if (this.cuIList[index].checked) {
						this.$set(this.cuIList[index], 'checked', false);
						this.cuIList[index].childrenList.forEach(item => {
							item.checked = false;
							count = 0;
						});
					} else {
						this.$set(this.cuIList[index], 'checked', true);
						this.cuIList[index].childrenList.forEach(item => {
							item.checked = true;
							if (item.quantity == '' || item.quantity == null) {
								count += Number(0);
							} else {
								count += Number(item.quantity);
							}
						});
					}
					this.cuIList[index].FCounty = count;
					this.$set(this.cuIList[index], 'show', true);
				} else {
					basic
						.inventoryByBarcode({
							uuid: this.cuIList[index].number + "," + this.cuIList[index].FBatchNo
						})
						/* .selectInvListByItemNumber({ itemNumber: this.cuIList[index].number }) */
						.then(reso => {
							if (reso.success) {
								let data = reso.data;
								this.cuIList[index].isLoading = true;
								data.forEach((item, index) => {
									item.quantity = 0;
									item.checked = false;
								});
								this.cuIList[index].childrenList = data;
								//选中一级目录的所有 FCounty
								let count = 0;
								if (this.cuIList[index].checked) {
									this.$set(this.cuIList[index], 'checked', false);
									this.cuIList[index].childrenList.forEach(item => {
										item.checked = false;
										console.log(item);
										if (item.quantity == '' || item.quantity == null) {
											count += Number(0);
										} else {
											count += Number(item.quantity);
										}
									});
								} else {
									this.$set(this.cuIList[index], 'checked', true);
									this.cuIList[index].childrenList.forEach(item => {
										item.checked = true;
										console.log(item);
										if (item.quantity == '' || item.quantity == null) {
											count += Number(0);
										} else {
											count += Number(item.quantity);
										}
									});
								}
								this.cuIList[index].FCounty = count;
								this.$set(this.cuIList[index], 'show', true);
								this.$forceUpdate();
							}
						})
						.catch(err => {
							uni.showToast({
								icon: 'none',
								title: err.msg
							});
						});
				}
			},
			_chooseOne(i1, i2) {
				if (this.cuIList[i1].childrenList[i2].checked) {
					//去掉勾选
					this.$set(this.cuIList[i1], 'checked', true);
					this.$set(this.cuIList[i1].childrenList[i2], 'checked', false);
					if (this.cuIList[i1].childrenList.every(item => item.checked == false)) {
						//判断是否全部都是选中
						this.$set(this.cuIList[i1], 'checked', false);
					}
					if (this.cuIList[i1].childrenList[i2].quantity == '' || this.cuIList[i1].childrenList[i2].quantity ==
						null) {
						return;
					} else {
						this.cuIList[i1].FCounty = this.cuIList[i1].FCounty - Number(this.cuIList[i1].childrenList[i2]
							.quantity);
					}
				} else {
					//增加勾选
					this.$set(this.cuIList[i1], 'checked', true);
					this.$set(this.cuIList[i1].childrenList[i2], 'checked', true);
					if (this.cuIList[i1].childrenList.every(item => item.checked == true)) {
						//判断是否全部都是选中
						this.$set(this.cuIList[i1], 'checked', true);
					}
					if (this.cuIList[i1].childrenList[i2].quantity == '' || this.cuIList[i1].childrenList[i2].quantity ==
						null) {
						return;
					} else {
						this.cuIList[i1].FCounty = this.cuIList[i1].FCounty + Number(this.cuIList[i1].childrenList[i2]
							.quantity);
					}
				}
			},
			_computedFinalList() {
				//计算最终的值
				this.finalList = [];
				this.cuIList.forEach(item => {
					if (item.checked) {
						this.finalList.push(JSON.parse(JSON.stringify(item))); //对象深拷贝 不然原数据会发生变化
					}
				});
				this.finalList.forEach(item => {
					item.childrenList.forEach((item2, index2) => {
						if (!item2.checked) {
							item.childrenList.splice(index2, 1);
						}
					});
				});
			},
			deleteItem(item, index) {
				let me = this;
				uni.showModal({
					title: '温馨提示',
					content: '是否删除当前行,删除将无法复原？',
					cancelText: '取消',
					confirmText: '确定',
					success: res => {
						if (res.confirm) {
							me.cuIList.splice(index, 1);
						}
					}
				});
			},
			cityClick(item) {
				this.form.FCustName = item.FName;
				this.form.FCustNumber = item.FNumber;
				this.modalName = null;
			},
			clearList() {
				const that = this;
				if (that.cuIList.length > 0) {
					uni.showModal({
						title: '温馨提示',
						content: '是否清空列表，清空之后将无法还原！',
						success: function(res) {
							if (res.confirm) {
								that.cuIList = [];
								that.initMain();
							} else if (res.cancel) {
								console.log('用户点击取消');
							}
						}
					});
				}
			},
			initMain() {
				const me = this;
				me.resultA = [];
				this.form.fdate = this.getDay('', 0).date;
				basic
					.getBillNo({
						TranType: 21
					})
					.then(res => {
						if (res.success) {
							me.form.finBillNo = res.data;
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
				basic
					.getDeptList({})
					.then(res => {
						if (res.success) {
							me.deptList = res.data;
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
				basic
					.getStockList({})
					.then(res => {
						if (res.success) {
							me.stockList = res.data;
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
				basic
					.customerList({})
					.then(res => {
						if (res.success) {
							me.customerList = res.data;
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
				me.loadModal = false;
				me.isClick = false;
			},
			saveData() {
				this.isClick = true;
				let portData = {};
				let result = [];
				let list = this.cuIList;
				let array = [];
				let isBatchNo = false;
				let batchMsg = '';
				let me = this;
				let cIndex = 0;
				for (let i in list) {
					let children = list[i].childrenList;
					children.forEach((item, index) => {
						if (item.checked) {
							cIndex++;
							let obj = {};
							obj.fauxqty = item.quantity;
							obj.fentryId = cIndex;
							obj.fcostobjid = me.form.FEntrySelf;
							obj.finBillNo = item.FBillNo;
							obj.fbatchNo = item.FBatchNo;
							/* if (list[i].FBatchManager) {
								if (list[i].fbatchNo != '' && list[i].fbatchNo != null) {
									obj.fbatchNo = list[i].fbatchNo;
									isBatchNo = true;
								} else {
									isBatchNo = false;
									batchMsg = '批号已启用，不允许为空';
									break;
								}
							} else {
								if (list[i].fbatchNo == '' || list[i].fbatchNo == null) {
									obj.fbatchNo = list[i].fbatchNo;
									isBatchNo = true;
								} else {
									batchMsg = '批号未启用，不允许输入';
									isBatchNo = false;
									break;
								}
							} */
							obj.fitemId = item.FNumber;
							obj.fdCSPId = item.FStockPlacename;
							obj.fauxprice = list[i].Fauxprice != null && typeof list[i].Fauxprice != 'undefined' ?
								list[i].Fauxprice : 0;
							obj.famount = list[i].Famount != null && typeof list[i].Famount != 'undefined' ? list[
								i].Famount : 0;
							obj.fdCStockId = item.FStockNumber;
							/* if (list[i].stockId == null || typeof list[i].stockId == 'undefined') {
								result.push(list[i].index);
							} */
							obj.fsourceBillNo = list[i].fsourceBillNo == null || list[i].fsourceBillNo ==
								'undefined' ? '' : list[i].fsourceBillNo;
							obj.fsourceEntryID = list[i].fsourceEntryID == null || list[i].fsourceEntryID ==
								'undefined' ? '' : list[i].fsourceEntryID;
							obj.fsourceTranType = list[i].fsourceTranType == null || list[i].fsourceTranType ==
								'undefined' ? '' : list[i].fsourceTranType;
							obj.funitId = item.FUnitID;
							array.push(obj);
						}
					});
				}
				portData.items = array;
				portData.ftranType = 21;
				portData.finBillNo = this.form.finBillNo;
				portData.fdate = this.form.fdate;
				portData.fbillerID = this.form.fbillerID;
				portData.fdeptId = this.form.fdeptID;
				portData.fcustId = this.form.FCustNumber;
				portData.fsupplyID = this.form.FCustNumber;
				if (this.form.FCustNumber == '' || typeof this.form.FCustNumber == 'undefined') {
					uni.showToast({
						icon: 'none',
						title: '客户不能为空'
					});
					this.isClick = false;
					return;
				}
				//if (result.length == 0) {
				if (portData.fcustId != '' && typeof portData.fcustId != 'undefined') {
					//if (isBatchNo) {
					sales
						.saleStockOut(portData)
						.then(res => {
							if (res.success) {
								this.cuIList = [];
								uni.showToast({
									icon: 'success',
									title: res.msg
								});
								this.form.bNum = 0;
								this.initMain();
								if (this.isOrder) {
									setTimeout(function() {
										uni.$emit('handleBack', {
											startDate: me.startDate,
											endDate: me.endDate,
											source: me.source
										});
										uni.navigateBack({
											url: '../sales/salesActive'
										});
									}, 1000);
								}
							}
						})
						.catch(err => {
							uni.showToast({
								icon: 'none',
								title: err.msg
							});
							basic
								.getBillNo({
									TranType: 21
								})
								.then(resBill => {
									if (resBill.success) {
										me.form.finBillNo = resBill.data;
									}
								})
								.catch(errBill => {
									uni.showToast({
										icon: 'none',
										title: errBill.msg
									});
								});
							this.isClick = false;
						});
					/* } else {
							uni.showToast({
								icon: 'none',
								title: batchMsg
							});
							this.isClick = false;
						} */
				} else {
					uni.showToast({
						icon: 'none',
						title: '客户不能为空'
					});
					this.isClick = false;
				}
				/* } else {
					uni.showToast({
						icon: 'none',
						title: '仓库不允许为空'
					});
					this.isClick = false;
				} */
			},
			submitCom() {
				var me = this;
				if (me.popupForm.positions != '' && me.popupForm.positions != null) {
					basic.selectFdCStockIdByFdCSPId({
						fdCSPId: me.popupForm.positions
					}).then(reso => {
						if (reso.data != null && reso.data != '') {
							if (reso.data['FIsStockMgr']) {
								me.borrowItem.stockName = reso.data['stockName'];
								me.borrowItem.stockId = reso.data['stockNumber'];
								me.borrowItem.FIsStockMgr = reso.data['FIsStockMgr'];
								me.borrowItem.positions = me.popupForm.positions;
								me.borrowItem.quantity = me.popupForm.quantity;
								me.borrowItem.fbatchNo = me.popupForm.fbatchNo;
								me.modalName2 = null;
							} else {
								me.borrowItem.stockName = reso.data['stockName'];
								me.borrowItem.stockId = reso.data['stockNumber'];
								me.borrowItem.FIsStockMgr = reso.data['FIsStockMgr'];
								me.borrowItem.positions = '';
								me.borrowItem.quantity = me.popupForm.quantity;
								me.borrowItem.fbatchNo = me.popupForm.fbatchNo;
								me.modalName2 = null;
							}
						} else {
							uni.showToast({
								icon: 'none',
								title: '该库位不存在仓库中！'
							});
						}
					});
				} else {
					if (me.popupForm.FIsStockMgr) {
						return uni.showToast({
							icon: 'none',
							title: '仓位已启用，请输入仓位！'
						});
					} else {
						me.borrowItem.positions = '';
						me.borrowItem.quantity = me.popupForm.quantity;
						me.borrowItem.fbatchNo = me.popupForm.fbatchNo;
						me.modalName2 = null;
					}
				}
			},
			saveCom() {
				var me = this;
				if (this.popupForm.quantity > me.borrowItem.Fauxqty) {
					uni.showModal({
						title: '温馨提示',
						content: '领料数量大于单据数量！请确认！',
						success: function(res) {
							if (res.confirm) {
								me.submitCom();
							} else if (res.cancel) {
								return;
							}
						}
					});
				} else {
					me.submitCom();
				}
			},
			saveCom2() {
				var me = this;
				if (this.popupForm.quantity > me.borrowItem.FQty) {
					uni.showToast({
						icon: 'none',
						title: '输入数量不能大于库存数量'
					});
				} else {
					me.borrowItem.quantity = me.popupForm.quantity
					me.modalName3 = null
				}
			},
			del(index, item) {
				this.cuIList.splice(index, 1);
				this.form.bNum = this.cuIList.length;
			},
			showModal(e) {
				this.modalName = e.currentTarget.dataset.target;
			},
			showModal4(e) {
				let me = this;
				console.log(this.isScanOf)
				if (me.isScanOf) {
					me.modalName4 = e.currentTarget.dataset.target;
				} else {
					uni.scanCode({
						success: function(res) {
							me.form.FEntrySelf = res.result;
						}
					});
				}
			},
			showModal2(index, item) {
				/* if (item.stockId == null || item.stockId == '') {
					return uni.showToast({
						icon: 'none',
						title: '请先选择仓库！'
					});
				} */
				this.modalName2 = 'Modal';
				if (item.fbatchNo == null || typeof item.fbatchNo == 'undefined') {
					item.fbatchNo = '';
				}
				if (item.positions == null || typeof item.positions == 'undefined') {
					item.positions = '';
				}
				if (item.quantity == null || typeof item.quantity == 'undefined') {
					item.quantity = '';
				}
				this.popupForm = {
					quantity: item.quantity,
					fbatchNo: item.fbatchNo,
					FIsStockMgr: item.FIsStockMgr,
					positions: item.positions
				};
				this.borrowItem = item;
			},
			showModal3(index, item) {
				/* if (item.stockId == null || item.stockId == '') {
					return uni.showToast({
						icon: 'none',
						title: '请先选择仓库！'
					});
				} */
				this.modalName3 = 'Modal';
				this.popupForm = {
					quantity: item.quantity,
				};
				this.borrowItem = item;
			},
			hideModal(e) {
				this.modalName = null;
			},hideModal4(e) {
				this.modalName4 = null;
			},
			hideModal2(e) {
				this.modalName2 = null;
				this.popupForm = {};
			},
			hideModal3(e) {
				this.modalName3 = null;
			},
			// 查询前后三天日期
			getDay(date, day) {
				var today = new Date();
				var targetday_milliseconds = today.getTime() + 1000 * 60 * 60 * 24 * day;
				today.setTime(targetday_milliseconds); //注意，这行是关键代码
				var tYear = today.getFullYear();
				var tMonth = today.getMonth();
				var tDate = today.getDate();
				var getDay = today.getDay();
				tMonth = this.doHandleMonth(tMonth + 1);
				tDate = this.doHandleMonth(tDate);
				var weeks = new Array('星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六');
				var week = weeks[getDay];
				return {
					day: tDate,
					week: week,
					date: tYear + '-' + tMonth + '-' + tDate
				};
			},
			doHandleMonth(month) {
				var m = month;
				if (month.toString().length == 1) {
					m = '0' + month;
				}
				return m;
			},
			deptChange(val) {
				this.form.fdeptID = val;
			},
			customerChange(val) {
				this.form.FCustNumber = val;
			},
			stockChange(val) {
				let sList = this.stockList;
				let list = this.cuIList;
				const me = this;
				for (let i in sList) {
					if (sList[i].FNumber == val) {
						for (let j in list) {
							me.$set(list[j], 'stockName', sList[i].FName);
							me.$set(list[j], 'FIsStockMgr', sList[i].FIsStockMgr);
							me.$set(list[j], 'FIsStockMgr', '');
							me.$set(list[j], 'stockId', val);
						}
					}
				}
			},
			bindChange(e) {
				this.form.fdate = e;
			},
			PickerChange(e, item) {
				this.$set(item, 'stockName', this.stockList[e.detail.value].FName);
				this.$set(item, 'stockId', this.stockList[e.detail.value].FNumber);
				this.$set(item, 'positions', '');
				this.$set(item, 'FIsStockMgr', this.stockList[e.detail.value].FIsStockMgr);
			},
			scanPosition() {
				let me = this;
				uni.scanCode({
					success: function(res) {
						basic.selectFdCStockIdByFdCSPId({
							fdCSPId: res.result
						}).then(reso => {
							if (reso.data != null && reso.data != '') {
								me.popupForm.positions = res.result;
								me.popupForm.stockName = reso.data['stockName'];
								me.popupForm.stockId = reso.data['stockNumber'];
								me.popupForm.FIsStockMgr = reso.data['FIsStockMgr'];
							} else {
								uni.showToast({
									icon: 'none',
									title: '该库位不存在仓库中！'
								});
							}
						});
					}
				});
			},
			chooseClick(val) {
				var that = this;
				var choose = val;
				let number = 0;
				if (val.length == 0) {
					that.resultA = [];
				}
				for (let j in choose) {
					if (that.isOrder) {
						for (let i in that.cuIList) {
							if (choose[j]['FItemID'] == that.cuIList[i]['FItemID']) {
								if (choose[j]['FStockNumber'] == that.cuIList[i]['stockId'] &&
									choose[j]['FBatchNo'] == that.cuIList[i]['fbatchNo'] &&
									choose[j]['FStockPlacename'] == that.cuIList[i]['positions']) {
									if (choose[j]['quantity'] == null) {
										choose[j]['quantity'] = 1;
									}
									if (choose[j]['isEnable'] == 2) {
										choose[j]['uuid'] = null;
									}
									if (that.cuIList[i]['quantity'] == choose[j]['FQty']) {
										that.cuIList[i]['quantity'] = parseFloat(that.cuIList[i]['quantity']) + parseFloat(
											choose[
												j]['quantity']);
									} else {
										uni.showToast({
											icon: 'none',
											title: '出库数量不能大于库存数量！'
										});
									}
									number++;
									break;
								}
							} else {
								uni.showToast({
									icon: 'none',
									title: '该物料不在所选列表中！'
								});
								number++;
								break;
							}
						}
						if (number == 0) {
							if (choose[j]['quantity'] == null) {
								choose[j]['quantity'] = 1;
							}
							if (choose[j]['isEnable'] == 2) {
								choose[j]['uuid'] = null;
							}
							choose[j].stockName = choose[j].FStockName;
							choose[j].stockId = choose[j].FStockNumber;
							choose[j].FIsStockMgr = choose[j].FIsStockMgr;
							choose[j].fbatchNo = choose[j].FBatchNo;
							choose[j].number = choose[j].FNumber;
							choose[j].name = choose[j].FName;
							choose[j].unitName = choose[j].FUnitName;
							choose[j].model = choose[j].FModel;
							choose[j].positions = choose[j].FStockPlacename;
							choose[j].unitID = choose[j].FUnitID;
							choose[j].maxQty = choose[j].FQty;
							that.cuIList.push(choose[j]);
							that.form.bNum = that.cuIList.length;
						}
						/* }else{
								uni.showToast({
									icon: 'none',
									title: '该物料不在所选单据中！',
								});
							} */
					} else {
						for (let i in that.cuIList) {
							if (
								choose[j]['FItemID'] == that.cuIList[i]['FItemID'] &&
								choose[j]['FStockNumber'] == that.cuIList[i]['stockId'] &&
								choose[j]['FBatchNo'] == that.cuIList[i]['fbatchNo'] &&
								choose[j]['FStockPlacename'] == that.cuIList[i]['positions']
							) {
								if (choose[j]['quantity'] == null) {
									choose[j]['quantity'] = 1;
								}
								if (choose[j]['isEnable'] == 2) {
									choose[j]['uuid'] = null;
								}
								if (Number(that.cuIList[i]['quantity']) < Number(choose[j]['FQty'])) {
									that.cuIList[i]['quantity'] = parseFloat(that.cuIList[i]['quantity']) + parseFloat(
										choose[
											j]['quantity']);
								} else {
									uni.showToast({
										icon: 'none',
										title: '出库数量不能大于库存数量！'
									});
								}
								number++;
								break;
							}
						}
						if (number == 0) {
							if (choose[j]['quantity'] == null) {
								choose[j]['quantity'] = 1;
							}
							if (choose[j]['isEnable'] == 2) {
								choose[j]['uuid'] = null;
							}
							choose[j].stockName = choose[j].FStockName;
							choose[j].stockId = choose[j].FStockNumber;
							choose[j].FIsStockMgr = choose[j].FIsStockMgr;
							choose[j].fbatchNo = choose[j].FBatchNo;
							choose[j].number = choose[j].FNumber;
							choose[j].name = choose[j].FName;
							choose[j].unitName = choose[j].FUnitName;
							choose[j].model = choose[j].FModel;
							choose[j].unitID = choose[j].FUnitID;
							choose[j].positions = choose[j].FStockPlacename;
							choose[j].maxQty = choose[j].FQty;
							that.cuIList.push(choose[j]);
							that.form.bNum = that.cuIList.length;
						}
					}
				}
				that.show = false
			},
			fabClick() {
				var that = this;
				let number = 0;
				uni.scanCode({
					success: function(res) {
						that.getScanInfo(res.result);
					}
				});
			},
			getScanInfo(res) {
				var that = this;
				let number = 0;
				let resData = [];
				if (res.split(';').length == 1) {
					resData[0] = res.split(',')[0]
					resData[1] = res.split(',')[1] + "," + res.split(',')[2] + "," + res.split(',')[3]
					resData[2] = res.split(',')[4]
				} else {
					resData = res.split(';')
				}
				basic
					.inventoryByBarcode({
						uuid: resData[0] + "," + resData[1]
					})
					.then(reso => {
						console.log(reso.data)
						if (reso.success) {
							/* that.chooseClick([reso.data[0]]); */
							let data = reso.data;
							that.chooseList = []
							for(let i in reso.data) {
								that.chooseList.push(reso.data[i])				
							} 
							that.show = true
						}
					})
					.catch(err => {
						uni.showToast({
							icon: 'none',
							title: err.msg
						});
					});
			},
			// ListTouch触摸开始
			ListTouchStart(e) {
				this.listTouchStart = e.touches[0].pageX;
			},

			// ListTouch计算方向
			ListTouchMove(e) {
				this.listTouchDirection = e.touches[0].pageX - this.listTouchStart > 0 ? 'right' : 'left';
			},

			// ListTouch计算滚动
			ListTouchEnd(e) {
				if (this.listTouchDirection == 'left') {
					this.modalName = e.currentTarget.dataset.target;
				} else {
					this.modalName = null;
				}
				this.listTouchDirection = null;
			}
		}
	};
</script>

<style>
	.cu-item {
		float: left;
		width: 50%;
	}

	.cu-item .content {
		float: left;
	}

	.cu-list.menu-avatar>.cu-item .content {
		left: 5px;
	}

	.cu-list.menu-avatar>.cu-item .action {}

	.input {
		height: 30px;
	}

	.box {
		width: 100%;
	}

	.uni-input-placeholder,
	.uni-input-input {
		font-size: 13px;
	}

	.action,
	.content {
		font-size: 13px !important;
	}

	.ruidata {
		font-size: 13px;
		height: 7vw !important;
	}

	.cu-bar {
		min-height: 30px;
	}

	/* .page {
		height: calc(100vh - 320upx);
	} */
	.nav-title::first-letter {
		font-size: 16px;
		margin-right: 2px;
	}
</style>
<style lang="scss" scoped>
	/* #ifdef APP-PLUS*/
	.selectTrees {
		margin-bottom: 180rpx;
	}

	/* #endif */

	.deleteBtn {
		position: absolute;
		right: 10%;
		background: #f97979;
		padding: 2rpx 16rpx;
		border-radius: 4rpx;
	}

	.itemT:nth-child(odd) {
		margin-left: 60rpx;
		color: #666666;
		width: 45% !important;
	}

	.itemT:nth-child(even) {
		color: #666666;
		width: 40% !important;
	}

	.itemO {
		color: #666666;
		width: 50% !important;
	}

	.tree-two {
		padding: 20rpx;
		color: #666666;
		border-bottom: 2rpx solid #e2e2e2;
	}

	.flexIn {
		display: flex;
		flex-direction: row;
		justify-content: flex-start;
		align-items: center;
		align-content: center;
		flex-wrap: nowrap;
		width: 100%;
	}
</style>
