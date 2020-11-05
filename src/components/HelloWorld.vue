<template>
  <div id="app">
    <header class="header">
      <el-row class="row-header" type="flex" justify="center">
        <el-col :span="15">
          <h2 class="header-h2">{{ pj_name }}</h2>
        </el-col>
      </el-row>
    </header>

    <div class="content">
      <el-row  type="flex" justify="center">
        <el-col :span="20" id="first">
          <h1 >背包问题</h1>
        </el-col>
      </el-row>
      <el-row  type="flex" justify="center">
        <el-col :span="20" class="second">
          <div class="intro">
            <p>
            背包问题(Knapsack problem)是一种组合优化的NP完全问题。问题可以描述为：
            <br />
            给定一组物品，每种物品都有自己的重量和价格，在限定的总重量内，我们如何选择，才能使得物品的总价格最高。
            </p>
            <el-button size="small" >
              <el-link href="https://baike.baidu.com/item/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98/2416931?fr=aladdin" target="_blank">
                View details >>
              </el-link>
            </el-button>
          </div>
        </el-col>
      </el-row>

      <el-row type="flex" justify="center" >
        <el-col :span="20" class="second">
          <el-form :inline="true" class="demo-form-inline" style="margin-top: 10px"> 
            <el-form-item label="背包大小：">
              <el-input-number
                v-model="size"
                @change="handleChange"
                :min="1"
                :max="100"
              ></el-input-number>
            </el-form-item>

            <el-form-item label="物品体积：" style="margin-left: 20px">
              <el-input-number
                v-model="item_size"
                :min="1"
                :max="100"
              ></el-input-number>
            </el-form-item>
            <el-form-item label="物品价值：">
              <el-input-number
                v-model="item_value"
                :min="1"
                :max="100"
              ></el-input-number>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="addItem">添加</el-button>
            </el-form-item>
            <el-button type="danger" style="margin-left:40px" @click="deleteAll">清空物品</el-button>
          </el-form>
          <div style="margin-bottom:10px">
            选中的物品：
            <el-radio v-model="radio" :label=false>不显示</el-radio>
            <el-radio v-model="radio" :label=true>显示</el-radio>
          </div>
        </el-col>
      </el-row>

      <el-row type="flex" justify="center">
        <el-col :span="20" class="second">
          <el-table :data="tableData" style="width: 100%" height="500" ref="table" :row-class-name="tableRowClassName">

            <el-table-column
              fixed
              prop="msg"
              label="背包大小"
              width="180"
              align="center"
            >
              <template slot-scope="scope">
                <span
                  >体积={{scope.row.item.size}}  价值={{
                    scope.row.item.value
                  }}</span
                >
                <el-button
                  type="danger"
                  icon="el-icon-delete"
                  circle
                  size="mini"
                  @click="handleDelete(scope.$index)"
                  :style="{display: scope.row.item.value == 0?'none':'inline'}"
                  style="margin-left: 10px"
                ></el-button>
              </template>
            </el-table-column>
            <el-table-column
              align="center"
              prop="dpvalue"
              v-for="(item, index) in parseInt(size) + 1"
              :key="index"
              :label="item - 1 + ''"
            >
              <template slot-scope="scope">
                <span>{{ scope.row.dpvalue[index] }}</span>
              </template>
            </el-table-column>
            
          </el-table>
        </el-col>
      </el-row>


      <el-row type="flex" justify="center">
        <el-col :span="20" class="second">
          <el-row type="flex" justify="center">
            <el-col :span="18" class="third">
              <!-- 背包大小 -->
              <el-card class="box-card">
                <div slot="header" class="clearfix" style="display: inline-block">
                    <i class="el-icon-suitcase"></i>
                    <span style="margin-left: 10px; font-weight: 700">背包大小</span>
                </div>
                {{this.size}}
                <br>
                <p style="text-align: left">已使用：{{this.bag_usage}}</p> 
                <el-progress :percentage="Math.floor(this.bag_usage*100/this.size)" :color="customColorMethod"></el-progress>
              </el-card>
              <!-- 物品价值 -->
              <el-card class="box-card">
                <div slot="header" class="clearfix">
                  <i class="el-icon-goods"></i>
                  <span style="margin-left: 10px; font-weight: 700">物品总价最大值</span>
                </div>
                  {{this.value_usage}}
                  <br>
                  <p style="text-align: left">价值占比：{{this.value_usage+'/'+this.total_value}}</p> 
                  <el-progress :percentage="this.tableData.length===1?0:Math.floor(this.value_usage*100/this.total_value)" :color="customColorMethod"></el-progress>
              </el-card>
              <!-- 选中的物品 -->
              <el-card class="box-card">
                <div slot="header" class="clearfix">
                  <i class="el-icon-shopping-cart-full"></i>
                  <span style="margin-left: 10px; font-weight: 700">选中的物品编号</span>
                </div>
                  <span v-for="o in this.selected" :key="o">{{o+' '}}</span>
                  <br>
                  <p style="text-align: left">选中数量占比：{{this.selected.length+'/'+(this.tableData.length-1)}}</p> 
                  <el-progress :percentage="this.tableData.length===1?0:Math.floor(this.selected.length*100/(this.tableData.length-1))" :color="customColorMethod"></el-progress> 
              </el-card>
            </el-col>
          </el-row>
        </el-col>
      </el-row>

      <el-row type="flex" justify="center">
        <el-col :span="20" id="copyright">
          <el-divider></el-divider>
          <p>© 2020 yyds, Inc.</p>
        </el-col>
      </el-row>
    </div>
  </div>
</template>

<script>
var size=101;
export default {
  data() {
    return {
      pj_name: "Visualize Knapsack",
      item_size: 1,
      item_value: 1,
      size: 10,
      bag_usage: 0,
      value_usage: 0,
      total_value: 0,
      dp: [[0]],
      selected: [],
      radio: false,
      tableData: [
        {
          item: {
            value: 0,
            size: 0,
          },
          dpvalue: new Int16Array(size),
        },
        // {
        //   item: {
        //     value: 5,
        //     size: 3,
        //   },
        //   dpvalue: [2, 2, 3, 4, 5, 6],
        // }
        ],
    };
  },
  methods: {
    // 改变背包大小
    handleChange() {
      this.$nextTick(() => {
        this.$refs.table.doLayout();
      });
      this.$options.methods.calculateDp(this);
    },
    //删除全部元素
    deleteAll() {
      this.bag_usage = 0;
      this.value_usage = 0;
      this.total_value = 0;
      this.selected = [];
      this.dp = [[0]];
      this.tableData = [
        {
          item: {
            value: 0,
            size: 0,
          },
          dpvalue: new Int16Array(size),
        },
      ];
    },
    // 删除物品
    handleDelete(index) {
      this.tableData.splice(index, 1);
      this.$options.methods.calculateDp(this);
    },
    // 动态规划
    calculateDp(self) {
      let weightList = [];
      weightList[0] = 0;
      let valueList = [];
      valueList[0] = 0;
      for (let i = 1; i <= self.tableData.length; i++) {
        weightList[i] = parseInt(self.tableData[i - 1].item.size);
        valueList[i] = parseInt(self.tableData[i - 1].item.value);
      }
      self.dp[0] = [];
      for (let i = 0; i <= self.size; i++) {
        self.dp[0][i] = 0;
      }
      for (let i = 1; i <= self.tableData.length; i++) {
        self.dp[i] = [];
        self.dp[i][0] = 0;
      }
      for (let i = 1; i <= self.tableData.length; i++) {
        for (let j = 1; j <= self.size; j++) {
          if (weightList[i] > j) {
            self.dp[i][j] = self.dp[i - 1][j];
          } else {
            let value1 = self.dp[i - 1][j - weightList[i]] + valueList[i];
            let value2 = self.dp[i - 1][j];
            if (value1 > value2) {
              self.dp[i][j] = value1;
            } else {
              self.dp[i][j] = value2;
            }
          }
        }
      }
      self.dp.splice(1, 1);
      self.$options.methods.backTrack(self);
      self.$options.methods.countUsage(self);
      for (let i = 1; i <= self.tableData.length; i++) {
            self.tableData[i].dpvalue = self.dp[i]
      }
    },
    addItem() {
      //赋值部分
      let tempObj = {
        item: {
          value: this.item_value,
          size: this.item_size,
        },
        dpvalue: new Int16Array(this.size+1),
      };
      this.tableData.push(tempObj);
      this.item_size = null;
      this.item_value = null;
      this.$options.methods.calculateDp(this);
    },
    backTrack(that){
      let j = that.size;
      that.selected = [];
      if (that.tableData.length < 2) {
        return '';
      }
      for (let i = that.tableData.length-1; i > 0; i--) {
        if(that.dp[i][j] != that.dp[i-1][j]){           
          j = j-that.tableData[i].item.size;
          that.selected.unshift(i);
        } 
      }
    },
    countUsage(that){
      let size_sum = 0;
      let value_sum = 0;
      let item_value = 0;
      for (const e of that.tableData) {
        item_value = item_value + e.item.value
      }
      for (const item of that.selected) {        
          size_sum = size_sum + that.tableData[item].item.size;
          value_sum = value_sum + that.tableData[item].item.value;
      }
      that.bag_usage = size_sum;
      that.value_usage = value_sum;
      that.total_value = item_value;
    },
    tableRowClassName({rowIndex}) {
      if(this.radio){
        if(this.selected.indexOf(rowIndex) > -1) {
          return 'success-row';
        } 
        else {
          return '';
        }
      }
      else{
        return '';
      }
    },
    customColorMethod(percentage) {
        if (percentage < 30) {
          return '#909399';
        } else if (percentage < 70) {
          return '#e6a23c';
        } else {
          return '#67c23a';
        }
      },
  },
};
</script>

<style>
.el-card {
  background: #fbfbfb;
  width: 300px;
  display: inline-block;
  margin: 0.3em;
  border-radius: 20px;
}
.grid-content {
  /* background: rgb(14, 214, 131); */
  border-radius: 4px;
  min-height: 80px;
}
.text {
  font-size: 14px;
}
.item {
  padding: 18px 0;
}
.header-h2 {
  color: #fdefd8;
  font-size: 2em;
  font-family: Arial, Helvetica, sans-serif;
}
.header-h2:hover {
  color: #fff;
}
.row-header {
  background: #409eff;
}
.row-header:hover {
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08), 0 6px 6px rgba(0, 0, 0, 0.1);
}
.content {
  background: rgb(245, 245, 245);
}
.header {
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.05), 0 6px 6px rgba(0, 0, 0, 0.05);
  z-index: 10;
  left: 0;
  right: 0;
  top: 0;
  margin-left: 8px;
  margin-right: 8px;
}
#first {
  background: rgb(255, 255, 255);
  z-index: 5;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08), 0 6px 6px rgba(0, 0, 0, 0.1);
}
.second {
  background: rgb(255, 255, 255);
  z-index: 5;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08), 0 6px 6px rgba(0, 0, 0, 0.1);
}
.second {
  background: rgb(255, 255, 255);
  z-index: 5;
}
.intro{
  background: rgb(255, 255, 255);
  text-align: center;
  margin:0 auto;
  width: 80%;
  margin-bottom: 1em;
}
#copyright {
  background: rgb(255, 255, 255);
  z-index: 5;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08), 0 6px 6px rgba(0, 0, 0, 0.1);
}
.el-table .warning-row {
    background: oldlace;
  }

.el-table .success-row {
  background: #f0f9eb;
}
</style>