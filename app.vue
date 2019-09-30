<template>
	<div class="center">
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
	</div>
</template>

<script>
  export default {
    data () {
      return {
        row: '', // 输入的行数
				col: '', // 输入的列数
				selectItems: [], // 选择单元格行为索引，值为列组成的数组
				rowCol: [], // 用于生成表格
				decription: '', // 是否是规则图形描述
				tableKey: '' // 用清除表格选中的样式
			}
		},
		methods: {
      // 给生成table的列赋值，重置相关参数
      productTable () {
        this.tableKey = new Date().getTime()
        this.selectItems = []
        this.rowCol = [this.row, this.col]
			},
			// 点击切换tr选中与否
      cellClick (i, j, e) {
        let classNames = e.target.getAttribute('class')
				if (!classNames) {
          e.target.setAttribute('class', 'yellow-bg')
					if (this.selectItems[i]) {
            this.selectItems[i].push(j)
					} else {
            this.selectItems[i] = [j]
					}
				} else {
          e.target.setAttribute('class', '')
          this.selectItems[i].splice(this.selectItems[i].indexOf(j), 1)
				}
			},
			// 方法1
      judgeShape () {
        let selectIndexs = []
				for (let i = 0; i < (this.rowCol[0] || 0); i++) {
          if (this.selectItems[i] && this.selectItems[i].length) {
            selectIndexs.push(i)
					}
				}
				let length = selectIndexs.length
        selectIndexs.sort()
				// 没有选择
				if (!length) {
          this.decription = ''
				} else if (length === 1) { // 只选择一行
          // 只有一列
					if (this.selectItems[selectIndexs[0]].length === 1) {
            this.decription = '规则的'
						return
					}
          // 判断col是否连续
					if (this.isSuccession(this.selectItems[selectIndexs[0]].sort())) {
            this.decription = '规则的'
					} else {
            this.decription = '不规则的'
					}
				} else if (!this.isSuccession(selectIndexs)) { // 判读row是否连续
          this.decription = '不规则的'
        } else {
          // 判断所有row数组是否一样长首位是否相等
          this.selectItems[selectIndexs[0]].sort()
					let itemLength = this.selectItems[selectIndexs[0]].length
          for (let i = 1; i < length; i++) {
            this.selectItems[selectIndexs[i]].sort()
            if (this.selectItems[selectIndexs[0]].length !== this.selectItems[selectIndexs[i]].length) {
              this.decription = '不规则的'
              return false
            } else if (this.selectItems[selectIndexs[0]][0] !== this.selectItems[selectIndexs[i]][0]) {
              this.decription = '不规则的'
              return false
						} else if (this.selectItems[selectIndexs[0]][itemLength] !== this.selectItems[selectIndexs[i]][itemLength]) {
              this.decription = '不规则的'
              return false
            }
          }
          this.decription = '规则的'
				}
			},
			// 判断数组是否连续
			isSuccession (item) {
        for (let i = 1; i < item.length; i++) {
          if (item[i - 1] + 1 !== item[i]) {
            return false
          }
        }
        return true
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