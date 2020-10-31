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
      <el-row style="height: 280px" type="flex" justify="center">
        <el-col :span="20" id="first">
          <br />
          <h2 style="font-size: 50px">Knapsack Computer</h2>
          <p>
            This is a template for a simple marketing or informational website.
            It includes a large callout called a jumbotron and three supporting
            pieces of content. Use it as a starting point to create something
            more unique.
          </p>
          <el-button type="primary">Learn more >></el-button>
          <br />
        </el-col>
      </el-row>

      <el-row type="flex" justify="center">
        <el-col :span="20" class="second">
          <el-form :inline="true" class="demo-form-inline">
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
          </el-form>
        </el-col>
      </el-row>

      <el-row type="flex" justify="center">
        <el-col :span="20" class="second">
          <el-table :data="tableData" style="width: 100%" height="500" ref="table">
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
          <el-card shadow="never" v-for="heading in headings" :key="heading">
            <h2>{{ heading }}</h2>
            <p>
              Donec id elit non mi porta gravida at eget metus. Fusce dapibus,
              tellus ac cursus commodo, tortor mauris condimentum nibh.
            </p>
            <el-button size="small">View details >></el-button>
          </el-card>
        </el-col>
      </el-row>

      <el-row type="flex" justify="center">
        <el-col :span="20" id="copyright">
          <el-divider></el-divider>
          <p>© 2020 Company, Inc.</p>
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
      headings: ["1Heading", "2Heading", "3Heading", "4Heading"],
      welcome: "Vue.js,Element-UI",
      pj_name: "Visualize Knapsack",
      item_size: 1,
      item_value: 1,
      size: 10,
      dp: [],
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
        //     value: 4,
        //     size: 4,
        //   },
        //   dpvalue: [1, 2, 3, 4, 5, 6],
        // },
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
    handleChange() {
      this.$nextTick(() => {
        this.$refs.table.doLayout();
      });
      this.$options.methods.calculateDp(this);
    },
    onSubmit() {
      console.log("submit!");
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
      for (let i = 1; i <= self.tableData.length; i++) {
            self.tableData[i].dpvalue = self.dp[i+1]
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
  margin-top: 50px;
  background: rgb(245, 245, 245);
}
.header {
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.05), 0 6px 6px rgba(0, 0, 0, 0.05);
  position: fixed;
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
#copyright {
  background: rgb(255, 255, 255);
  z-index: 5;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.08), 0 6px 6px rgba(0, 0, 0, 0.1);
}
</style>