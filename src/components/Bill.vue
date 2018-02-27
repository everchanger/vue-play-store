<template>
	<div class="bill" :class="className">
		<div class="inner-bill" >
			{{value}}
			<div class="bill-marker">
			</div>
		</div>
	</div>
</template>

<style>
.bill {
	content: '';
	width: 125px;
	height: 50px;
	border: 2px solid #292929;
	padding: 4px;
	background-color: rgb(255, 30, 98);
	box-shadow: 1px 2px 6px 0px rgba(0, 0, 0, 0.31);
	border-radius: 2px;
}

.inner-bill {
	position: relative;
	display: flex;
	align-items: flex-end;
	height: 100%;
	width: 100%;
	border: 2px solid #292929;
	background-color: rgb(255, 255, 255);
	color:#292929;
	padding: 2px;
	font-weight: bold;
	font-size: 1.5em;
	line-height: 1.0;
	border-radius: 2px;
	box-shadow: inset 0px 5px 25px 0px rgba(0, 0, 0, 0.13);
}

.bill-marker {
	position: absolute;
	right: -3px;
	top: -3px;
	content: '';
	border: 4px solid #3f3f3f;
	border-radius: 50px;
	background-color: rgba(0, 0, 0, 0.09);
	height: 116%;
	width: 36%;
}

.bill-one-hundred .inner-bill{
	background-color: #acb2ff;
}

.bill-fifty .inner-bill{
	background-color: #aae076;
}

.bill-twenty .inner-bill{
	background-color: #d4b291;
}

</style>

<script>
export default {
	name: 'bill',
	props: {
		value: {
			type: Number,
			required: true,
		},
	 },
	 computed: {
		 className() {
			let billClass = 'none';
			switch (this.value) {
				case 100:
					billClass = 'bill-one-hundred';
				break;
				case 50:
					billClass = 'bill-fifty';
				break;
				case 20:
					billClass = 'bill-twenty';
				break;
				default:
					console.log(this.value);
				break;
			}

			 return billClass;
		 },
	 },
};
</script>
