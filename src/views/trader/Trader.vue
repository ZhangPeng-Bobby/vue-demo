<template>
  <div>
    <el-card>
      <el-button
        size="medium"
        @click="addNew()">Add New Leg
      </el-button>
    </el-card>
    <el-table
      :data="traderLeg"
      style="width: 100%"
      border stripe>
      <el-table-column
        prop="txnId"
        label="TXN ID"
      >
      </el-table-column>
      <el-table-column
        prop="interVNum"
        label="version"
      >
      </el-table-column>
      <el-table-column
        prop="sId"
        label="sellerId"
      >
      </el-table-column>
      <el-table-column
        prop="cusip"
        label="cusip">
      </el-table-column>
      <el-table-column
        prop="price"
        label="price">
      </el-table-column>
      <el-table-column
        prop="notionalAmount"
        label="amount">
      </el-table-column>
      <el-table-column
        prop="status"
        label="status">
      </el-table-column>
      <el-table-column
        label="option" width="260">
        <template slot-scope="scope">
          <el-button
            size="mini"
            @click="handleHistory(scope.$index, scope.row)">History
          </el-button>
          <el-button
            size="mini"
            @click="handleEdit(scope.$index, scope.row)">Edit
          </el-button>
          <el-button
            size="mini"
            type="danger"
            @click="handleMatch(scope.$index, scope.row)">Match
          </el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-dialog
      title="traderLeg history"
      :visible.sync="dialogVisible"
      width="50%"
      center>
      <el-table
        :data="traderLegHistory"
        style="width: 100%"
        border>
        <el-table-column
          prop="interVNum"
          label="version"
        >
        </el-table-column>
        <el-table-column
          prop="sId"
          label="sellerId"
        >
        </el-table-column>
        <el-table-column
          prop="cusip"
          label="cusip">
        </el-table-column>
        <el-table-column
          prop="price"
          label="price">
        </el-table-column>
        <el-table-column
          prop="notionalAmount"
          label="amount">
        </el-table-column>
        <el-table-column
          prop="status"
          label="status">
        </el-table-column>
        <!--        <el-table-column-->
        <!--          label="option">-->
        <!--          <template slot-scope="scope">-->
        <!--            <el-button-->
        <!--              size="mini"-->
        <!--              type="danger"-->
        <!--              @click="handleForceMatch(scope.$index, scope.row)">match-->
        <!--            </el-button>-->
        <!--          </template>-->
        <!--        </el-table-column>-->
      </el-table>
      <span slot="footer" class="dialog-footer">
<!--    <el-button @click="dialogVisible = false">cancel</el-button>-->
    <el-button type="primary" @click="dialogVisible = false">ok</el-button>
  </span>
    </el-dialog>
    <el-dialog
      title="unMatched salesLeg"
      :visible.sync="matchDialogVisible"
      width="50%"
      center>
      <el-table
        :data="salesLeg"
        style="width: 100%"
        border
        stripe
      >
        <el-table-column
          prop="txnId"
          label="TXN ID"
        ></el-table-column>
        <el-table-column
          prop="txnId"
          label="TXN ID"
        ></el-table-column>
        <el-table-column
          prop="sId"
          label="sId"
        >
        </el-table-column>
        <el-table-column
          prop="cId"
          label="clientId"
        >
        </el-table-column>
        <el-table-column
          prop="cusip"
          label="cusip">
        </el-table-column>
        <el-table-column
          prop="price"
          label="price">
        </el-table-column>
        <el-table-column
          prop="notionalAmount"
          label="amount">
        </el-table-column>
        <el-table-column
          prop="status"
          label="status">
        </el-table-column>
        <el-table-column
          label="option">
          <template slot-scope="scope">
            <el-button
              size="mini"
              type="danger"
              @click="handleForceMatch(scope.$index, scope.row)">match
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <span slot="footer" class="dialog-footer">
    <el-button @click="matchDialogVisible = false">cancel</el-button>
    <el-button type="primary" @click="matchDialogVisible = false">ok</el-button>
  </span>
    </el-dialog>
    <el-dialog
      title="add new traderLeg"
      :visible.sync="addDialogVisible"
      width="50%"
      center
    >
      <el-form :model="ruleForm" :rules="rules" ref="ruleForm" label-width="auto" class="demo-ruleForm"
               style="width: 100%; margin:0 auto;">
        <el-form-item label="Cusip" prop="cusip">
          <el-select :disabled="disabledController" v-model="ruleForm.cusip" placeholder="choose the cusip" value=""
                     :clearable="true"
                     style="width: 100%">
            <el-option
              v-for="item in cusip"
              :key="item"
              :label="item"
              :value="item">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Seller" prop="sId">
          <el-select :disabled="disabledController" v-model="ruleForm.sId" placeholder="choose your name" value=""
                     :clearable="true"
                     style="width: 100%">
            <el-option
              v-for="item in seller"
              :key="item.sId"
              :label="item.sName"
              :value="item.sId">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Price" prop="price">
          <el-input v-model.number="ruleForm.price" placeholder="please input the price"></el-input>
        </el-form-item>
        <el-form-item label="Notional Amount" prop="notionalAmount">
          <el-input v-model.number="ruleForm.notionalAmount" placeholder="please input the amount"></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="submitForm('ruleForm')">submit</el-button>
          <el-button @click="resetForm('ruleForm')">reset</el-button>
        </el-form-item>
      </el-form>

    </el-dialog>
  </div>
</template>
<script>
  export default {
    data () {
      return {
        dialogVisible: false,
        disabledController: false,
        matchDialogVisible: false,
        addDialogVisible: false,
        traderLeg: [],
        traderLegHistory: [],
        salesLeg: [],
        allTraderLeg: [],
        chosenTraderLeg: {},
        chosenSalesLeg: {},
        cusip: [],
        seller: [],
        client: [],
        ruleForm: {
          cusip: '',
          sId: '',
          price: '',
          notionalAmount: ''
        },
        rules: {
          cusip: [
            {required: true, message: 'please choose a product', trigger: 'change'}
          ],
          sId: [
            {type: 'date', required: true, message: 'please choose your name', trigger: 'change'}
          ],
          notionalAmount: [
            {required: true, message: 'please input the notional amount', trigger: 'blur'},
            {type: 'number', message: 'not a number', trigger: 'blur'}
          ],
          price: [
            {required: true, message: 'please input a price', trigger: 'blur'},
            {type: 'number', message: 'not a number', trigger: 'blur'}
          ]
        }
      }

    },
    beforeCreate () {
      this.$nextTick(function () {
        let _this = this
        this.$http.get('/all-cusip').then(res => {
          _this.cusip = res.data
          // console.log(res)
        })
        this.$http.get('/all-seller').then(res => {
          _this.seller = res.data
          // console.log(res)
        })
        this.$http.get('/all-client').then(res => {
          _this.client = res.data
          // console.log(res)
        })
        this.$http.get('/trader-leg').then(res => {
          _this.allTraderLeg = res.data
          console.log(_this.allTraderLeg)
          _this.$http.get('/newest-trader-leg').then(res => {
            _this.traderLeg = []
            res.data.map(function (obj) {
              if (obj.status === 'PENDING') {
                _this.traderLeg.push(obj)
              }
            })
          })
        })
      })
    },
    methods: {
      handleMatch (index, rowData) {
        let _this = this
        _this.matchDialogVisible = true
        _this.chosenTraderLeg = rowData
        _this.$http.get('/newest-sales-leg').then(res => {
          //user clicked one traderLeg row
          //then click another, we need clear the salesLeg array
          // console.log(res.data + "!!!!!!!!!!!!!!!!")
          _this.salesLeg = []
          res.data.map(function (obj) {
            if (obj.status === 'PENDING') {
              _this.salesLeg.push(obj)
            }
          })
        })
      },
      handleHistory (index, rowData) {
        let _this = this
        _this.dialogVisible = true
        console.log(rowData.txnId)
        _this.$http.post('/tw-txn-history', rowData.txnId).then(res => {
          _this.traderLegHistory = res.data
        })
      },
      handleEdit (index, rowData) {
        let _this = this
        _this.addDialogVisible = true
        _this.ruleForm = rowData
        _this.disabledController = true
      },
      handleForceMatch (index, rowData) {
        let _this = this
        _this.chosenSalesLeg = rowData
        let param = '/force-match?traderLegTxnId=' + _this.chosenTraderLeg.txnId + '&salesLegTxnId=' + _this.chosenSalesLeg.txnId
        _this.$http.get(param).then(res => {
          if (res.data === false) {

            _this.$message.err('failed!')
          } else {
            _this.$message.success('force match success!')
            _this.matchDialogVisible = false
            _this.$http.get('/newest-trader-leg').then(res => {
              _this.traderLeg = []
              res.data.map(function (obj) {
                if (obj.status === 'PENDING') {
                  _this.traderLeg.push(obj)
                }
              })
            })
          }
        })

        _this.dialogVisible = false
      },
      addNew () {
        this.addDialogVisible = true
        this.disabledController = false
        this.ruleForm = {
          cusip: '',
          sId: '',
          price: '',
          notionalAmount: '',
          status: 'REQUESTED',
          interVNum: 1,
          interId: 'TW1'
        }
      },
      submitForm (formName) {
        let _this = this
        this.$refs[formName].validate((valid) => {
          if (valid) {
            // console.log(JSON.stringify(_this.ruleForm))
            console.log(JSON.stringify(_this.ruleForm))
            if (_this.ruleForm.status !== 'REQUESTED') {
              _this.ruleForm.status = 'PENDING'
              _this.ruleForm.interVNum = _this.ruleForm.interVNum + 1
              _this.ruleForm.interId = 'TW' + _this.ruleForm.interVNum
            }
            _this.$http.post('/trader-leg', _this.ruleForm).then(res => {
              _this.addDialogVisibletrader = false
              console.log(res)
              _this.$message.success('submit success!')
              if(res.data===1){
                this.$http.get('/trader-leg').then(res => {
                  _this.allTraderLeg = res.data
                  _this.$http.get('/newest-trader-leg').then(res => {
                    _this.traderLeg = []
                    res.data.map(function (obj) {
                      if (obj.status === 'PENDING') {
                        _this.traderLeg.push(obj)
                      }
                    })
                  })
                  this.addDialogVisible = false
                })
              }else {
                _this.$router.replace('/matchedLeg')
                this.$refs[formName].resetFields()
              }

            })

          } else {
            _this.$message.error('error submit!!')
            return false
          }
        })
      },
      resetForm (formName) {
        this.$refs[formName].resetFields()
      }
    }
  }
</script>
<style>


</style>
