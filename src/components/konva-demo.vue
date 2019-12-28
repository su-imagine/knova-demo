<template>
	<div>
		<h1 class="title">KONVA DEMO</h1>
		<div class="konva-wrapper">
			<div id="konva-container">
				
			</div>
		</div>		
	</div>

</template>
<style scoped>
	.title{
		background-color: orange;
		color: #fff;
		height: 50px;
		line-height: 50px;
	}
	.konva-wrapper{
		width: 1000px;
		height: 500px;
		background-color: #33ffcc;
	}
	#konva-container{
		width: 100%;
		height: 100%;
		background-color: #33ffcc;
	}
</style>
<script>
import Konva from 'konva'
// import { VERTEXSTYLE } from ''
 

export default {
	data(){
		return {
			konvaWidth:'',
			konvaHeight:'',
			stage:null,
			backgroundLayer:null,
			shapeLayer:null,
			clickTag:0,
			dbCickFlag:false,
			shapeConfig:{
				path:[]
			},
			circleArr:[]
		}
	},
	methods:{
		init(){
			let konvaContainer = document.getElementById('konva-container')
			this.konvaWidth = konvaContainer.clientWidth
			this.konvaHeight = konvaContainer.clientHeight

			this.stage = new Konva.Stage({
				container:'konva-container',
				width:this.konvaWidth,
				height:this.konvaHeight
			})

			this.backgroundLayer = new Konva.Layer({
				x:0,
				y:0,
				width:this.konvaWidth,
				height:this.konvaHeight
			})

			this.stage.add(this.backgroundLayer)

			this.shapeLayer = new Konva.Layer({
				x:0,
				y:0,
				width:this.konvaWidth,
				height:this.konvaHeight
			})

			this.stage.add(this.shapeLayer)

			this.backgroundLayer.setZIndex(0)
			this.shapeLayer.setZIndex(1)
			this.stage.on('click',e => {
				const ev = e.evt
				const x = ev.clientX
				const y = ev.clientY - 50 // 减去 tab 栏的高度

				// 判断是否在可操作的区域内
				// if(){
					// todo
				// }

				// 点击过快则为双击
				if(this.clickTag === 0) {
					this.clickTag = 1
					setTimeout(() => {
						this.clickTag = 0
					},500)
				}else{
					this.dbCickFlag = true
				}

				this.shapeConfig.path.push([x,y])

				this.drawFence()

			})

			this.stage.on('mousemove',e => {
				const ev = e.evt
				const x = ev.clientX
				const y = ev.clientY
				this.shapeConfig = {
					...this.shapeConfig,
					endX : x,
					endY : y - 50
				}
				this.drawFence()
			})

		},

		drawFence(){
			const { shapeConfig } = this
			let tempPath = []
			let path = []
			tempPath = [...shapeConfig.path,[shapeConfig.endX,shapeConfig.endY]]

			tempPath.map(item => {
				if(path.length === 0){
					path.push(item)
				}else if(!path.find(p => p[0] === item[0] && p[1] === item[1])){
					path.push(item)
				}
			})

			path.push(path[0])  // 首尾相连


			// 画端点

			let dotArr = []
			path.map(item => {
				let dot = new Konva.Circle({
					x: item[0],
				    y: item[1],
				    radius: 4,
				    fill: 'rgb(255,255,255)',
				    stroke: 'green',
				    strokeWidth: 1,
				    draggable:true
				})
				dotArr.push(dot)
				this.circleArr.push(dot)
			})

			let ploygon = new Konva.Shape({
				sceneFunc:(context,shape) => {
					context.beginPath()
					path.map((item,index) => {
						if(index === 0){
							context.moveTo(item[0],item[1])
						}else{
							context.lineTo(item[0],item[1])
						}
					})
					context.closePath()
					context.fillStrokeShape(shape)
				},
				fill: 'rgba(0, 132, 255, 0.2)',
    			stroke: '#4C98FF',
    			strokeWidth: 2,
    			draggable:true
			})


			const transformer = new Konva.Transformer()


			this.shapeLayer.destroyChildren()

			this.shapeLayer.add(transformer)
			transformer.attachTo(ploygon)
			dotArr.map(dot => {
				transformer.attachTo(ploygon)
			})

			this.shapeLayer.add(ploygon)
			dotArr.map(dot => this.shapeLayer.add(dot))
			this.shapeLayer.batchDraw()

			if(this.dbCickFlag){
				this.stage.removeEventListener('click')
				this.stage.removeEventListener('mousemove')

				// this.stage.on('click',e => {
				// 	console.log(e)
				// 	console.log(this.shapeConfig.path)
				// 	const ev = e.evt
				// 	const path = this.shapeConfig.path
				// 	let pointX = ev.clientX
				// 	let pointY = ev.pointY
				// 	const ev = e.evt
				// 	this.shapeConfig.path[0][0] = ev.clientX
				// 	this.shapeConfig.path[0][1] = ev.clientY - 50
				// })

				// this.stage.on('mousemove',e => {
				// 	const ev = e.evt
				// })
			}

		}
	},
	mounted(){
		this.init()
	}
};
</script>

