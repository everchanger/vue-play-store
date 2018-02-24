<template>
	<v-layout>
		<div v-if="! scanMode">
			<div class="row register-header">
					<h1>Ica Stop</h1>
			</div>
			<div class="row register-container justify-content-md-center">
				<div class="register-scan-area col-sm-12 col-md-6">
					<ul>
						<li class="scanned-item header">
							<div>
								Ikon
							</div>
							<div>
								Produkt
							</div>
							<div>
								Pris
							</div>
							<div>
								Ta bort
							</div>
						</li>
						<li class="scanned-item" v-for="(scan, index) in scans" :key="scan.date" :title="scan.content">
							<div>
								<i :class="iconClass(scan.icon)" ></i>
							</div>
							<div>
								{{ scan.product }}
							</div>
							<div>
								{{ scan.price }}	
							</div>
							<button class="btn btn-xs btn-danger" @click="scans.splice(index, 1);">X</button>
						</li>
					</ul>
					<div v-for="(count, cur) in currency" class="total-sum">
						<div v-for="c in Number(count)">
							<!--<v-bill v-if="cur > 10 && count"  :value="Number(cur)"/>-->
							<v-coin v-if="cur < 20 && count" :value="Number(cur)"/>
						</div>
					</div>						
					<button class="btn btn-lg btn-primary" @click="scanMode = true">Skanna</button>
				</div>
			</div>
		</div>
		<div v-else class="row register-container">
			<div class="register-scan-area col-12">
				<button class="btn btn-lg btn-danger btn-exit" @click="scanMode = false">Tillbaka</button>
				<video class="scanner-preview" ref="preview"></video>
				<h2>Kameror</h2>
				<ul>
					<li v-if="cameras.length === 0" class="empty">Hittade ingen kamera</li>
					<li v-for="camera in cameras" :key="camera.id">
						<span v-if="camera.id == activeCamera.id" :title="formatName(camera.name)" class="active">{{ formatName(camera.name) }}</span>
						<button v-if="camera.id != activeCamera.id" class="btn btn-sm btn-primary" :title="formatName(camera.name)" @click="selectCamera(camera)">
							{{ formatName(camera.name) }}
						</button>
					</li>
				</ul>
			</div>
		</div>
	</v-layout>
</template>

<style>
	ul {
		padding: 0;
	}
	li {
		list-style: none;
	}
	.header {
		font-weight: bold;
		margin-bottom: 10px;
		border-bottom: 1px solid #707070;
	}

	.total-sum {
		display: flex;
	}

	.total-sum > div { 
		display: flex;
		align-items: center;
		margin: 0 10px 10px 0;
		font-weight: bold;
	}
	.scanned-item {
		display: flex;
		justify-content: space-between;
		margin-bottom: 10px;
	}

	.active {
		font-weight: bold;
		color: #00FF00;
	}

	.register-header {
		padding-top: 10px;
		margin-bottom: 10px;
	}

	.scanner-preview {
		width: 100%;
		padding: 2px;
		background-color: #000000;
		border: 2px solid #bbbbbb;
		border-radius: 5px;
	}

	.register-scan-area {
		padding: 10px;
		background-color: #d1d1d1;
		border: 1px solid #8f8f8f;
		border-radius: 5px;
	}

	.btn-exit {
		margin-bottom: 5px;
	}
</style>

<script>
/* eslint-disable */
import Vue from 'vue';
import { Instascan } from '@/plugins/instascan';
import VLayout from '@/layouts/Minimal';
//import VBill from '@/components/Bill';
import VCoin from '@/components/Coin';

const g_currency = [
	100,
	50,
	20,
	10,
	5,
	2,
	1
]

export default {
	 name: 'login-index',
	 data() {
		return {
			scanner: null,
			scans: [],
			activeCamera: null,
			cameras: [],
			scanMode: false,
			currency: {
				1: 0,
				2: 0,
				5: 0,
				10: 0,
				20: 0,
				50: 0,
				100: 0,
			},
		};
	 },
	 methods: {
		formatName: function (name) {
			return name || '(unknown)';
		},
		selectCamera: function (camera) {
			this.activeCamera = camera;
			this.scanner.start(camera);
		},
		parseProductData(data) {
			if(! data.includes('butik:')) {
				return;
			}

			const arr = data.split(':');

			const scan = {
				icon: '',
				product: '',
				price: '',
			};

			scan.icon = arr[1];
			scan.product = arr[2];
			scan.price = arr[3];

			this.populateCurrency(Number(scan.price));

			this.scans.push(scan);
		},
		iconClass(iconName) {
			return 'fa ' + iconName;
		},
		populateCurrency(price) {
			for (let cur in this.currency) {
				cur = 0;
			}

			for (let i = 0; i < g_currency.length; ++i) {
				const cur = g_currency[i];
				while (1) {
					const future = price - cur;
					if (future > cur) {
						console.log(future + ' ' + cur);
						this.currency[cur]++;
						price = future;
					} else {
						if(future >= 0) {
							this.currency[cur]++;
							price = future;
						}
						break;
					}
				}
			}

			console.log(this.currency);
		}
	 },
	 components: {
		VLayout,
		//VBill,
		VCoin,
	 },
	 mounted() {
		const self = this;
		this.$instascan.Camera.getCameras().then(function cam(cameras) {
				self.cameras = cameras;
				if (cameras.length > 0) {
					self.activeCamera = cameras[0];
				} else {
					console.error('No cameras found.');
				}
				}).catch(function err(e) {
				console.error(e);
			});
	},
	watch: {
		scanMode(to) {
			if(this.scanMode) {
				const self = this;
				Vue.nextTick(function () {
						self.scanner = new self.$instascan.Scanner(
						{
							video: self.$refs.preview,
							mirror: false,
						},
					);
					self.scanner.start(self.activeCamera);
					self.scanner.addListener('scan', function scan(content) {
						self.parseProductData(content);
						self.scanMode = false;
						console.log(content);
					});
				});
			} else {
				this.scanner.stop(this.activeCamera);
			}
		},
	},
};
</script>
