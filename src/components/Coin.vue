<template>
	<div class="coin" :class="className">
		<div class="inner-coin" >
			{{value}}
		</div>
	</div>
</template>

<style>
.coin {
	content: '';
	width: 50px;
	height: 50px;
	border: 2px solid #292929;
	padding: 4px;
	background-color: rgb(255, 30, 98);
	box-shadow: 1px 2px 6px 0px rgba(0, 0, 0, 0.31);
	border-radius: 50px;
}

.inner-coin {
	position: relative;
	display: flex;
	align-items: center;
	justify-content: center;
	height: 100%;
	width: 100%;
	border: 2px solid #292929;
	background-color: rgb(255, 255, 255);
	color:#292929;
	padding: 2px;
	font-weight: bold;
	font-size: 1.5em;
	line-height: 1.0;
	border-radius: 50px;
	box-shadow: inset 0px 5px 25px 0px rgba(0, 0, 0, 0.13);
}

.coin-one {
	width: 40px;
	height: 40px;
}

.coin-two, .coin-five {
	width: 45px;
	height: 45px;
}


.coin-one .inner-coin{
	background-color: #ff9bf7;
}
.coin-two .inner-coin{
	background-color: #77d884;
}
.coin-five .inner-coin{
	background-color: #8eccff;
}
.coin-ten .inner-coin{
	background-color: #fff09b;
}

</style>

<script>
export default {
	name: 'coin',
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
				case 1:
					billClass = 'coin-one';
				break;
				case 2:
					billClass = 'coin-two';
				break;
				case 5:
					billClass = 'coin-five';
				break;
				case 10:
					billClass = 'coin-ten';
				break;
				default:
				break;
			}

			 return billClass;
		 },
	 },
};
</script>
