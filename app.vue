<template>
	<div class="center">
		<h3>方法一：</h3>
		<div class="margin-bottom">
			<input v-model.number="row" type="number"/>
			<input v-model.number="col" type="number"/>
			<button @click="productTable">生成表格</button>
		</div>
		<div class="table-content" v-if="rowCol[0] && rowCol[1]">
			<table :key="tableKey">
				<tr>
					<th v-for="n in rowCol[1]" :key="n"></th>
				</tr>
				<tr v-for="i in rowCol[0]" :key="i">
					<td @click="cellClick(i - 1, j - 1, $event)" v-for="j in rowCol[1]" :key="i + j">{{`${i - 1},${j - 1}`}}</td>
				</tr>
			</table>
		</div>
		<div class="margin-bottom">
			<button @click="judgeShape">点击判断</button>
		</div>
		<div>
			所选择表格组成的图形是：<span class="red">{{decription}}</span>
		</div>
		<h3>方法二：</h3>
		<second></second>
	</div>
</template>

<script>
  import Second from './second.vue'
  export default {
    components: { Second },
    data () {
      return {
        row: '', // 输入的行数
				col: '', // 输入的列数
        selectItemNum: 0, // 选择的单元格个数
				selectCoordinates: new Map(), // 用于存储每个单元格四个角的坐标
				rowCol: [], // 用于生成表格
				decription: '', // 是否是规则图形描述
				tableKey: '' // 用清除表格选中的样式
			}
		},
		methods: {
      // 给生成table的列赋值，重置相关参数
      productTable () {
        this.decription = ''
				this.selectItemNum = 0
        this.tableKey = new Date().getTime()
        this.selectCoordinates = new Map()
        this.rowCol = [this.row, this.col]
			},
			// 点击切换tr选中与否,并记录坐标
      cellClick (i, j, e) {
        let classNames = e.target.getAttribute('class')
				if (!classNames) {
          this.selectItemNum += 1
          e.target.setAttribute('class', 'yellow-bg')
          this.selectCoordinates.set(i + '' + j, (this.selectCoordinates.get(i + '' + j) || 0) + 1)
          this.selectCoordinates.set((i + 1) + '' + j, (this.selectCoordinates.get((i + 1) + '' + j) || 0) + 1)
          this.selectCoordinates.set(i + '' + (j + 1), (this.selectCoordinates.get(i + '' + (j + 1)) || 0) + 1)
          this.selectCoordinates.set((i + 1) + '' + (j + 1), (this.selectCoordinates.get((i + 1) + '' + (j + 1)) || 0) + 1)
				} else {
          this.selectItemNum -= 1
          e.target.setAttribute('class', '')
          this.selectCoordinates.set(i + '' + j, (this.selectCoordinates.get(i + '' + j) || 0) - 1)
          this.selectCoordinates.set((i + 1) + '' + j, (this.selectCoordinates.get((i + 1) + '' + j) || 0) - 1)
          this.selectCoordinates.set(i + '' + (j + 1), (this.selectCoordinates.get(i + '' + (j + 1)) || 0) - 1)
          this.selectCoordinates.set((i + 1) + '' + (j + 1), (this.selectCoordinates.get((i + 1) + '' + (j - 1)) || 0) - 1)
				}
			},
			// 方法1
      judgeShape () {
        let rowIndexArr = []
				let colIndexArr = []
        this.selectCoordinates.forEach((value, key) => {
          if (value > 0) {
            let arr = key.split('')
            rowIndexArr.push(arr[0])
            colIndexArr.push(arr[1])
					}
				})
				// 排序
        rowIndexArr.sort()
        colIndexArr.sort()
				// 左上角的点存在且只有一个
				if (this.selectCoordinates.get(rowIndexArr[0] + '' + colIndexArr[0]) !== 1) {
          this.decription = '不规则的'
				} else if (this.selectCoordinates.get(rowIndexArr[rowIndexArr.length - 1] + '' + colIndexArr[colIndexArr.length - 1]) !== 1) {
          // 右下角的点存在且只有一个
          this.decription = '不规则的'
        } else {
          // 长乘宽等于选择的单元格数
          let rowLenth = rowIndexArr[rowIndexArr.length - 1] - rowIndexArr[0]
          let colLenth = colIndexArr[colIndexArr.length - 1] - colIndexArr[0]
					if (rowLenth * colLenth !== this.selectItemNum) {
            this.decription = '不规则的'
					} else {
            this.decription = '规则的'
					}
				}
			}
		}
  }
</script>

<style scoped>
	.center {
		text-align: center;
	}
	table {
		border-collapse: collapse;
	}
	td,th {
		height: 30px;
		width: 60px;
		border-top:1px solid #999;
		text-align: center;
	}
	th{
		background-color: beige;
	}
	.margin-bottom {
		margin-bottom: 20px;
	}
	.red {
		color: red;
	}
	.table-content {
		margin: 0 40px 20px;
		border: 1px solid black;
	}
	.yellow-bg {
		background-color: yellow;
	}
</style>