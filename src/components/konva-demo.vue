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
					this.shapeConfig.path.push([x,y])
					setTimeout(() => {
						this.clickTag = 0
					},500)
				}else{
					this.dbCickFlag = true
				}
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
				}else if(!path.find(p => p[0] === item[0] && p[1] === item[1])){  // 没有去重
					path.push(item)
				}
			})

			// path.push(path[0])  // 首尾相连


			// 画端点

			let dotArr = []
			path.map((item,index) => {
				if(path.length < 2 ){
					return
				}
				if(item[0] === path[0][0] && item[1] === path[1]){
					return
				}
				let dot = new Konva.Circle({
					x: item[0],
				    y: item[1],
				    radius: 8,
				    fill: 'rgb(255,255,255)',
				    stroke: 'green',
					strokeWidth: 1,
					name:'circle',
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

			this.shapeLayer.destroyChildren()

			this.shapeLayer.add(ploygon)
			
			dotArr.map(dot => this.shapeLayer.add(dot))
			this.shapeLayer.batchDraw()

			if(this.dbCickFlag){
				this.stage.removeEventListener('click')
				this.stage.removeEventListener('mousemove')
				if(!this.hasDone){
					this.hasDone = true
					this.stage.on('mouseup',e => {
						this.stage.removeEventListener('mousemove')
					})

					this.stage.on('mousedown',e => {
						let target = e.target
						if(target.attrs.name === 'circle'){
							let circleArr = this.stage.find('Circle')
							let ind = circleArr.findIndex((circle,index) => {
								return circle._id === target._id
							})
							this.stage.on('mousemove',e => {
								let ev = e.evt
								let x = ev.clientX
								let y = ev.clientY - 50
								this.shapeConfig.path[ind] = [x,y]
								this.reDrawFence()
							})
						}
					})
				}
			}			
		},
		reDrawFence(){
			const { shapeConfig } = this
			let tempPath = []
			let path = []
			// tempPath = [...shapeConfig.path,[shapeConfig.endX,shapeConfig.endY]]
			tempPath = [...shapeConfig.path]

			tempPath.map(item => {
				if(path.length === 0){
					path.push(item)
				}else if(!path.find(p => p[0] === item[0] && p[1] === item[1])){  // 没有去重
					path.push(item)
				}
			})

			// path.push(path[0])  // 首尾相连


			// 画端点

			let dotArr = []
			path.map((item,index) => {
				// if(path.length < 3 ){
				// 	return
				// }
				let dot = new Konva.Circle({
					x: item[0],
				    y: item[1],
				    radius: 8,
				    fill: 'rgb(255,255,255)',
				    stroke: 'green',
					strokeWidth: 1,
					name:'circle',
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

			this.shapeLayer.destroyChildren()

			this.shapeLayer.add(ploygon)
			
			dotArr.map(dot => this.shapeLayer.add(dot))
			this.shapeLayer.batchDraw()
		}
	},
	mounted(){
		this.init()
	}
};
</script>

