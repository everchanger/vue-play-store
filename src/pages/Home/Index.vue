<template>
	<v-layout>
		<div class="row register-header">
				<h1><i class="fa fa-shopping-basket"></i> <span class="ica">ICA</span> <span class="ica_stop">Stop</span></h1>
		</div>
		<transition name="fade" mode="out-in" v-on:after-enter="toggleScanMode">
			<div v-if="! scanMode" key="register" >
				<div class="row register-container justify-content-md-center">
					<transition name="fade" mode="out-in">
						<div v-if="scans.length" class="register-scan-area col-sm-12" >
							<ul  key="has-items">
								<transition-group name="fade">
									<li class="scanned-item" v-for="(scan, index) in scans" :key="scan.date" :title="scan.content">
										<div class="product-info">
											<div class="product-name">
												{{ scan.product }}
											</div>
											<div class="product-price">
												{{ scan.price }} kr
											</div>
											<div>
												<button class="btn btn-xs btn-danger shop-btn" @click="scans.splice(index, 1);"><i class="fa fa-trash fa-3x"></i></button>
											</div>
										</div>
										<div class="currency">
											<div v-for="(count, cur) in scan.currency" v-if="count" class="currency-row">
												<div v-for="c in Number(count)">
													<v-bill v-if="cur > 10 && count"  :value="Number(cur)"/>
													<v-coin v-if="cur < 20 && count" :value="Number(cur)"/>
												</div>
											</div>	
										</div>
										
									</li>
								</transition-group >
							</ul>
						</div>
					</transition>
					<button class="btn btn-lg btn-primary scan-button shop-btn" @click="scanMode = true; fakePlay();"><i class="fa fa-cart-plus fa-2x"></i></button>
				</div>
			</div>
			<div v-if="scanMode" class="row register-container" key="scanner">
				<div class="register-scan-area col-12">
					<button class="btn btn-lg btn-danger btn-exit shop-btn" @click="scanMode = false;"><i class="fa fa-times-circle fa-2x"></i></button>
					<div class="pull-right">
						<span v-for="(camera, index) in cameras" :key="camera.id">
							<button class="btn btn-secondary camera-btn shop-btn active" v-if="camera.id == activeCamera.id" disabled><i class="fa fa-camera fa-2x"> </i> {{ index + 1 }}</button>
							<button v-if="camera.id != activeCamera.id" class="btn btn-primary camera-btn shop-btn" @click="selectCamera(camera)">
								<i class="fa fa-camera fa-2x"> </i> {{ index + 1 }}
							</button>
							<p v-if="cameras.length === 0" class="empty">Hittade ingen kamera</p>
						</span>
					</div>
					<video class="scanner-preview" ref="preview"></video>
				</div>
			</div>
		</transition>
	</v-layout>
</template>

<style>
	h1 {
		font-weight: bold;
	}
	ul {
		padding: 0;
		margin: 0;
	}
	li:last-child {
		padding-bottom: 0px;
		margin-bottom: 0px;
	}
	li {
		list-style: none;
		background-color: #ffc671;
		border-radius: 5px;
		border: 2px solid #292929;
		padding: 10px;
		padding-left: 15px;
	}
	.header {
		font-weight: bold;
		margin-bottom: 10px;
		border-bottom: 1px solid #707070;
	}

	.product-info {
		display: flex;
		flex-direction: column;
	}

	.ica {
		color: #00F;
	}

	.ica_stop {
		color: #F00;
	}

	.shop-btn {
		border: 2px solid #292929 !important;
	}

	.scan-button {
		width: 100%;
		margin-top: 10px;
	}
	
	.camera-btn {
		margin: 2px;
		font-size: 1.25em;
	}

	.empty-register {
		font-weight: bold;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.empty-register p {
		margin: 0;
	}

	.currency {
		display: flex;
		flex-direction: column-reverse;
		width: 65%;
		
	}

	.currency-row { 
		display: flex;
		padding: 10px;
		font-weight: bold;
		flex-wrap: wrap;
		width: 100%;
	}

	.currency-row > div {
		width: 30%;
		margin-right: 3%;
		margin-bottom: 3%;
	}

	.scanned-item {
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 10px;
	}

	.product-name {
		font-size: 2.5em;
		font-weight: bold;
		color: #00ceff;
		text-shadow:
		-1px -1px 0 #000,  
		1px -1px 0 #000,
		-1px 1px 0 #000,
		1px 1px 0 #000;
	}

	.product-price {
		font-size: 2em;
		font-weight: bold;
		color: #1efff6;
		text-shadow:
		-1px -1px 0 #000,  
		1px -1px 0 #000,
		-1px 1px 0 #000,
		1px 1px 0 #000;

		margin-bottom: 10px;
	}

	.active {
		font-weight: bold;
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
		background-color: #ff9900;
		border: 2px solid #292929;
		border-radius: 5px;
	}

	.btn-exit {
		margin-bottom: 5px;
	}

	.fade-enter-active, .fade-leave-active {
		transition: opacity .125s;
	}
	.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
		opacity: 0;
	}
</style>

<script>
/* eslint-disable */
import Vue from 'vue';
import { Instascan } from '@/plugins/instascan';
import VLayout from '@/layouts/Minimal';
import VBill from '@/components/Bill';
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
			scannerBeep: new Audio(require('@/assets/audio/scanner.mp3')),
		};
	 },
	 methods: {
		selectCamera: function (camera) {
			this.activeCamera = camera;
			this.scanner.start(camera);
		},
		parseProductData(data) {
			if(! data.includes('butik:')) {
				return;
			}

			const arr = data.split(':');
			const seconds = new Date().getTime() / 1000;

			const scan = {
				product: '',
				price: '',
				currency: {
					1: 0,
					2: 0,
					5: 0,
					10: 0,
					20: 0,
					50: 0,
					100: 0,
				},
				date: seconds,
			};

			scan.product = arr[1];
			scan.price = arr[2];

			this.populateCurrency(scan);

			this.scans.push(scan);

			this.scannerBeep.volume = 1.0;
			this.scannerBeep.play();
		},
		iconClass(iconName) {
			return 'fa ' + iconName;
		},
		fakePlay() {
			this.scannerBeep.volume = 0.0;
			this.scannerBeep.play();
		},
		populateCurrency(scan) {
			let price = Number(scan.price);
			for (let i = 0; i < g_currency.length; ++i) {
				const cur = g_currency[i];
				while (1) {
					const future = price - cur;
					if (future >= cur) {
						console.log(future + ' ' + cur);
						scan.currency[cur]++;
						price = future;
					} else {
						if(future >= 0) {
							scan.currency[cur]++;
							price = future;
						}
						break;
					}
				}
			}

			console.log(scan.currency);
		},
		toggleScanMode() {
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
				if (this.scanner) {
					this.scanner.stop(this.activeCamera);
				}
			}
		}
	 },
	 components: {
		VLayout,
		VBill,
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

		/*this.parseProductData('butik:Oboy:156');
		this.parseProductData('butik:Makaroner:456');
		this.parseProductData('butik:Falukorv:98');*/
	},
	watch: {
		scanMode(to) {
			
		},
	},
};
</script>
