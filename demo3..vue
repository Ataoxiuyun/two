<template>
  <div class="box">
    <!-- <h1>慢性病</h1> -->
    <el-form :inline="true"  class="demo-form-inline">
      <el-form-item label="姓名">
        <el-input v-model="pName"  placeholder="姓名"></el-input>
      </el-form-item>
      <el-form-item label="身份证号">
        <el-input v-model="pId" placeholder="身份证号"></el-input>
      </el-form-item>
      <el-form-item label="疾病名称">
          <el-select v-model="disease" placeholder="请选择">
          <el-option

            v-for="  (item ,index) in options"
            :key="index"
            :label="item.label"
            :value="item.label">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="完成状态">
        <el-select v-model="screenList" placeholder="请选择">
          <el-option
            v-for="  (item ,index) in options1"
            :key="index"
            :label="item.label"
            :value="item.label">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="queryUser">查询</el-button>
      
      </el-form-item>
    </el-form>
   
        <el-table  :data="tableData"  stripe  v-loading='loading' style="width: 100%">
          <el-table-column prop="pName"  label="姓名"  width="80"> </el-table-column>
          <el-table-column prop="hname"  label="检查机构名称"  width="140"> </el-table-column>
          <el-table-column  prop="PId"  label="身份证号" width="200"></el-table-column>
          <el-table-column  prop="address"  label="家庭住址" width="260"></el-table-column>
          <el-table-column  prop="link_phone"  label="联系方式" width="140"></el-table-column>
          <el-table-column  prop="ipType"  label="疾病名称" width="120"></el-table-column>
          <el-table-column prop="" label="状态">
            <template slot-scope="scope">
              <div>
                {{scope.row.accomplish=='1' ? '已完成' : '未完成'}}
              </div>
            </template>
          </el-table-column>
          <el-table-column   label="操作" width="260px">
            <template slot-scope="scope">
              <div class="btn">
                <el-button type="danger" size="small" @click="details(scope.row)">详情</el-button>
                <!-- <el-button type="success" size="small" @click="amend(scope.row)">修改</el-button> -->
                <el-button type="primary" size="small" @click="screeningList(scope.row)">并发症筛查列表</el-button>
              
              </div>
            </template>
          </el-table-column>
        </el-table>
        <el-pagination @size-change="handleSizeChange" @current-change="handleCurrentChange"
                     :current-page="currentPage" :page-sizes="[10, 20, 30, 40]" :page-size="pageSize"
                     layout="total, sizes, prev, pager, next, jumper" :total="total">
      </el-pagination>
      
    <!-- 详情-->
    <el-dialog     title="详情" :visible.sync="dialogVisible1" width="50%" >  
      <el-form :inline="true" class="demo-form-inline" style="text-align: left;">
        <ul class="helper_tablist1" style="border:none">
            <li>
                <span style="font-size:16px;">姓名：{{particulars.name}}</span>
                <span style="font-size:16px;">身份证号：{{particulars.idno}}</span>
                
            </li>
            <li>
                <span style="font-size:16px;">是否有糖尿病史：{{particulars.tnb==true ? '有'  : '无'}}</span>
                <span style="font-size:16px;">空腹血糖值：{{particulars.kfxt}}mmoI/L</span>
            </li>
           
            <li>
                <span style="font-size:16px;">身高：{{particulars.sg}}cm</span>
                <span style="font-size:16px;">糖化血红蛋白HbA1c：{{particulars.hbA1c}}mmoI/L</span>
            </li>
            <li>
                <span style="font-size:16px;">体重：{{particulars.tz}}KG</span>
                <span style="font-size:16px;">是否有高血脂病史：{{particulars.gxz==true ? '有'  : '无'}}</span>
            </li>
            <li>
                <span style="font-size:16px;">体重指数：{{particulars.tzzs}}BMI</span>
                <span style="font-size:16px;">甘油三脂TG：{{particulars.tg}}mmoI/L</span>
            </li>
            <li>
                <span style="font-size:16px;">是否有高血压病史：{{particulars.gxy==true ? '有'  : '无'}}</span>
                <span style="font-size:16px;">总胆固醇TG：{{particulars.tc}}mmoI/L</span>
            </li>
            <li>
                <span style="font-size:16px;">高压：{{particulars.gy}} mm/Hg</span>
                <span style="font-size:16px;">高密度脂蛋白胆固醇HDL-c：{{particulars.hdlC}}mmoI/L</span>
            </li>
            <li>
                <span style="font-size:16px;">低压：{{particulars.dy}}mm/Hg</span>
                <span style="font-size:16px;">低密度脂蛋白胆固醇HDL-c：{{particulars.ldlC}}mmoI/L</span>
            </li>
             <li>
                <!-- <span style="font-size:16px;">所属人群：{{particulars.ipTypeOther}}</span> -->
                <span style="font-size:16px;">餐后2h血糖值：{{particulars.ch2hxt}}mmoI/L</span>
            </li>
        </ul>
    
      </el-form>
    </el-dialog>
    <!-- 修改 -->
    <!-- <el-dialog     title="修改" :visible.sync="dialogVisible2" width="50%" >   
      <el-form :inline="true" class="demo-form-inline" style="text-align: left;">
        <ul class="dialogSty">
          <li>
            <div>
              <p>姓名：</p>
              <el-input v-model="input" placeholder="请输入姓名"></el-input>

            </div>
            <div>
              <p>是否有糖尿病史：</p>
              <el-radio-group  v-model="radio">
                <el-radio  label="1">有</el-radio>
                <el-radio  label="2">无</el-radio>
              </el-radio-group>
            </div>
          </li>
          <li>
            <div>
              <p>身份证号：</p>
              <el-input v-model="input" placeholder="请输入身份证号"></el-input>
            </div>
            <div>
              <p>空腹血糖值：</p>
              <el-input v-model="input" placeholder="请输入空腹血糖值"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
            <div>
              <p>所属人群：</p>
              <el-select v-model="crowd" placeholder="请选择">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
            <div>
              <p>餐后2h血糖值：</p>
              <el-input v-model="input" placeholder="请输入餐后2h血糖值"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
            <div>
              <p>身高：</p>
              <el-input v-model="input" placeholder="请输入身高"></el-input><span>cm</span>
            </div>
            <div>
              <p>糖化血红蛋白HbA1c：</p>
              <el-input v-model="input" placeholder="请输入糖化血红蛋白HbA1c"></el-input><span>mmoI/L</span>  
            </div>
          </li>
          <li>
            <div>
              <p>体重：</p>
              <el-input v-model="input" placeholder="请输入体重"></el-input><span>KG</span>
            </div>
            <div>
              <p>是否有高血脂病史：</p>
              <el-radio-group  v-model="radio1">
                <el-radio  :label="1">有</el-radio>
                <el-radio  :label="2">无</el-radio>
              </el-radio-group>
             
            </div>
          </li>
          <li>
            <div>
              <p>体重指数：</p>
              <el-input v-model="input" placeholder="请输入体重指数"></el-input><span>BMI</span>
            </div>
            <div>
              <p>甘油三脂TG：</p>
               <el-input v-model="input" placeholder="请输入甘油三脂TG"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
            <div>
              <p>是否有高血压病史：</p>
              <el-radio-group  v-model="radio2">
                <el-radio  :label="1">有</el-radio>
                <el-radio  :label="2">无</el-radio>
              </el-radio-group>
             
            </div>
            <div>
              <p>总胆固醇TG：</p>
              <el-input v-model="input" placeholder="请输入总胆固醇TG"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
            <div>
              <p>高血压：</p>
             <el-input v-model="input" placeholder="高压" style="width:20%"></el-input><span class="slash" >/</span><el-input v-model="input" placeholder="低压" style="width:20%"></el-input><span>mm/Hg</span>
            </div>
            <div>
              <p>高密度脂蛋白胆固醇HDL-c：</p>
             <el-input v-model="input" placeholder="请输入高密度脂蛋白胆固醇HDL-c"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
         
            <div>
              <p>文化程度：</p>
              <el-select v-model="crowd" placeholder="请选择">
                <el-option
                  v-for="item in options"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
            </div>
            <div>
              <p>低密度脂蛋白胆固醇HDL-c：</p>
              <el-input v-model="input" placeholder="请输入低密度脂蛋白胆固醇HDL-c"></el-input><span>mmoI/L</span>
            </div>
          </li>
          <li>
            <div>
              <p>腰围：</p>
              <el-input v-model="input" placeholder="请输入腰围"></el-input>
            </div>
            <div>
              <p>臀围：</p>
              <el-input v-model="input" placeholder="请输入臀围"></el-input>
            </div>
          </li>
          <li>
            <div>
              <p>心率：</p>
              <el-input v-model="input" placeholder="请输入心率"></el-input>
            </div>
            <div>
              <p>生活方式：</p>
              <el-input v-model="input" placeholder="请输入生活方式"></el-input>
            </div>
          </li>
          <li>
            
          </li>
        </ul>
        <div class="preservation">
          <el-button type="primary" >保存</el-button>
        </div>
      </el-form>
    </el-dialog> -->
    <!-- 并发症 -->
    <el-dialog     :title="title" :visible.sync="dialogVisible3" width="50%" :before-close="handleDialogClose" >
      <div v-if="title=='新增' ">
        <span>检查时间：</span>
        <el-date-picker
          v-model="createDate"
          type="date"
          placeholder="选择日期">
        </el-date-picker>
      </div>
       <el-tabs v-model="activeName" @tab-click="handleClick">
         <el-tab-pane :label="item.label" :name="index + '' " v-for="(item,index) in paneData" :key="index">
              <!-- 冠心病 -->
              <div v-if="item.label=='冠心病' ">
                <ul class="dialogSty">
                      <li>
                        <div>
                          <p>冠心病家族史：</p>
                          <el-radio-group v-model="coronaryDisease.gxbFamily">
                            <el-radio  :label="1">有</el-radio>
                            <el-radio  :label="0">无</el-radio>
                          </el-radio-group>
                        </div>
                        <div>
                          <p>冠状动脉造影：</p>
                          <el-input v-model="coronaryDisease.gxbAngiography" placeholder="请输入冠状动脉造影"></el-input>
                        </div>
                      </li>
                      <li>
                        <div>
                          <p>慢性肾病：</p>
                          <el-input v-model="coronaryDisease.gxbKidneyDisease" placeholder="请输入慢性肾病"></el-input>
                        </div>
                        <div>
                          <p>尿白蛋白：</p>
                          <el-input v-model="coronaryDisease.gxbAlb" placeholder="请输入尿白蛋白"></el-input>
                        </div>
                      </li>
                      <li>
                        <div>
                          <p>血肌钙蛋白：</p>
                          <el-input v-model="coronaryDisease.gxbTroponin" placeholder="请输入血肌钙蛋白"></el-input><span>ng/mL</span>
                        </div>
                        <div>
                          <p>肌酸激酶：</p>
                          <el-input v-model="coronaryDisease.gxbCreatineKinase" placeholder="请输入肌酸激酶"></el-input><span>U/L</span>
                        </div>
                      </li>
                      <li>
                        <div>
                          <p>冠状动脉CTA：</p>
                          <el-input v-model="coronaryDisease.gxbCta" placeholder="请输入冠状动脉CTA"></el-input>
                        </div>
                        <div>
                          <p>肌酸激酶龚同酶：</p>
                          <el-input v-model="coronaryDisease.gxbIsoenzyme" placeholder="请输入肌酸激酶龚同酶"></el-input>
                        </div>
                      </li>
                      <!-- <li>
                        <div>
                          <p>检查时间：</p>
                           <el-date-picker
                            v-model="coronaryDisease.gxbDate"
                            type="date"
                            :disabled="title=='修改' ? true : false "
                            placeholder="选择日期">
                          </el-date-picker>
                        </div>
                      </li> -->
                      <div class="imgStyle">
                    <span>心电图：</span>
                    <!--   class="upload-demo" -->
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="xdtRemove"
                      :before-remove="beforeRemove"
                      multiple
                      :limit="1"
                      :on-success="xdtSuccess"
                      :on-exceed="handleExceed1"
                      :file-list="gxbFilelist1">
                      <el-button size="small" type="primary" :disabled="fileLength==1 ? true: false ">+上传附件</el-button>
                    </el-upload>
                   <span>诊断说明：</span> <el-input v-model="coronaryDisease.gxbElectrocardiogram" ></el-input>
                  </div>
                  <div class="imgStyle">
                    <span>心脏彩超：</span>
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="xzccRemove"
                      :before-remove="beforeRemove"
                      :on-success="xzccSuccess"
                      multiple
                      :limit="1"
                      :on-exceed="handleExceed"
                      :file-list="gxbFilelist2">
                      <el-button size="small" type="primary" :disabled="xzccLength==1 ? true : false ">+上传附件</el-button>
                    </el-upload>
                    <span>诊断说明：</span> <el-input v-model="coronaryDisease.gxbUcg" ></el-input>
                  </div>
                </ul>
                <div class="preservation">
                    <el-button type="primary"  @click="preservation">保存</el-button>
                  </div>
              </div>
              <!-- 脑卒中 -->
              <div v-else-if="item.label=='脑卒中' " class="tabItme">
                <!-- <el-form  :inline="true"  >
                  <el-form-item label="高胆固醇血症：" prop="redProtein" >
                    <el-radio-group v-model="radio">
                      <el-radio  :label="1">有</el-radio>
                      <el-radio  :label="2">无</el-radio>
                    </el-radio-group>
                  </el-form-item>

                  <el-form-item label="心房颤动：" prop="redProtein" >
                    <el-radio-group v-model="radio">
                      <el-radio  :label="1">有</el-radio>
                      <el-radio  :label="2">无</el-radio>
                    </el-radio-group>
                  </el-form-item>

                  <el-form-item label="家族史父亲有脑卒中：" prop="redProtein" >
                    <el-radio-group v-model="radio">
                      <el-radio  :label="1">有</el-radio>
                      <el-radio  :label="2">无</el-radio>
                    </el-radio-group>
                  </el-form-item>

                  <el-form-item label="家族史父亲有冠心病：" prop="redProtein" >
                    <el-radio-group v-model="radio">
                      <el-radio  :label="1">有</el-radio>
                      <el-radio  :label="2">无</el-radio>
                    </el-radio-group>
                  </el-form-item>
                  <el-form-item label="家族史母亲有脑卒中：" prop="redProtein" >
                    <el-radio-group v-model="radio">
                      <el-radio  :label="1">有</el-radio>
                      <el-radio  :label="2">无</el-radio>
                    </el-radio-group>
                  </el-form-item>

                </el-form> -->
                <ul class="dialogSty stroke">
                    <li>
                      <div>
                        <p>高胆固醇血症：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzHypercholesterolemia">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      
                      </div>
                      <div>
                        <p>心房颤动</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzFibrillation">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                    </li>
                    <li>
                    
                      <div>
                        <p>家族史父亲有脑卒中：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzFartherNzx">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                      <div>
                      <p>家族史父亲有冠心病：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzFartherGxb">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                    </li>
                    <li>
                      
                      <div>
                        <p>家族史母亲有脑卒中：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzMotherNzx">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                      <div>
                        <p>家族史母亲有冠心病：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzMotherGxb">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                    </li>
                    <li>
                      <div>
                        <p>家族史兄弟姐妹有脑卒中：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzBrotherNzx">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                      <div>
                        <p>家族史兄弟姐妹有冠心病：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzBrotherGxb">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                    </li>
                    <li>
                      <div>
                        <p>家族史兄子女有脑卒中：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzChildrenNzx">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                      <div>
                        <p>家族史子女有冠心病：</p>
                        <el-radio-group v-model="cerebralApoplexy.nzzChildrenGxb">
                          <el-radio  :label="1">有</el-radio>
                          <el-radio  :label="0">无</el-radio>
                        </el-radio-group>
                      </div>
                    </li>
                    <li>
                      <div>
                        <p>日吸烟量：</p>
                        <el-input v-model="cerebralApoplexy.nzzSmokingDaily" ></el-input>
                      </div>
                    
                        <!-- <div>
                          <p>创建时间：</p>
                           <el-date-picker
                            v-model="cerebralApoplexy.nzzDate"
                            type="date"
                            :disabled="title=='修改' ? true : false "
                            placeholder="选择日期">
                          </el-date-picker>
                        </div> -->
                      
                    </li>
                  </ul>
                  <div class="imgStyle">
                    <span>颈动脉超声：</span>
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="handleRemove"
                      :before-remove="beforeRemove"
                      :on-success="handleAvatarSuccess"
                      multiple
                      :limit="1"
                      :on-exceed="handleExceed"
                      :file-list="fileList">
                      <el-button size="small"  :disabled="jdmLength==1 ? true: false " type="primary">+上传附件</el-button>
                    
                    </el-upload>
                    <span>诊断说明：</span> <el-input v-model="cerebralApoplexy.nzzUltrasound" ></el-input>
                  </div>
                  <div class="imgStyle">
                    <span>颅脑CT</span>
                    <!--   class="upload-demo" -->
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="handleRemove2"
                      :before-remove="beforeRemove"
                      :on-success="handleAvatarSuccess2"
                      multiple
                      :limit="1"
                      :on-exceed="handleExceed"
                      :file-list="fileList2">
                      <el-button size="small" :disabled="ctLength==1 ? true: false " type="primary">+上传附件</el-button>
                    
                    </el-upload>
                    <span>诊断说明：</span> <el-input v-model="cerebralApoplexy.nzzCt" ></el-input>
                  </div>
                  <div class="imgStyle">
                    <span>颅脑MRI：</span>
                    <!--   class="upload-demo" -->
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="handleRemove3"
                      :before-remove="beforeRemove"
                      :on-success="handleAvatarSuccess3"
                      multiple
                      :limit="1"
                      :on-exceed="handleExceed"
                      :file-list="fileList3">
                      <el-button size="small" :disabled="MRILength==1 ? true: false " type="primary">+上传附件</el-button>
                    
                    </el-upload>
                    <span>诊断说明：</span> <el-input v-model="cerebralApoplexy.nzzMri" ></el-input>
                  </div>
                  <div class="imgStyle">
                    <span>颅脑MRA：</span>
                    <!--   class="upload-demo" -->
                    <el-upload
                    class="upload-demo"
                      action="/threeHignManager/Inteworkst/uploadImg"
                      :on-preview="handlePreview"
                      :on-remove="handleRemove4"
                      :before-remove="beforeRemove"
                      :on-success="handleAvatarSuccess4"
                      multiple
                      :limit="1"
                      :on-exceed="handleExceed"
                      :file-list="fileList4">
                      <el-button size="small" :disabled="MRALength==1 ? true: false " type="primary">+上传附件</el-button>
                    
                    </el-upload>
                    <span>诊断说明：</span> <el-input v-model="cerebralApoplexy.nzzMra" ></el-input>
                  </div>
                  <div class="preservation">
                    <el-button type="primary"  @click="preservation">保存</el-button>
                  </div>
              </div>
              <!-- 肾脏病 -->
              <div v-else-if="item.label=='肾脏病' || item.label=='糖尿病肾病' ">
                <el-form  :inline="true" :model="ruleForm2"  ref="ruleForm2"   :rules="rules2" >
                  <el-form-item label="血常规血红蛋白：" prop="szbHemoglobin" >
                    <el-input v-model="ruleForm2.szbHemoglobin" placeholder="请输入血常规血红蛋白"></el-input>
                  </el-form-item>
                  <el-form-item label="血常规白细胞：" prop="szbHemameba" >
                    <el-input v-model="ruleForm2.szbHemameba" placeholder="请输入血常规白细胞"></el-input>
                  </el-form-item>
                  <el-form-item label="血常规血小板：" prop="szbPlatelet" >
                    <el-input v-model="ruleForm2.szbPlatelet" placeholder="请输入血常规血小板"></el-input>
                  </el-form-item>
                  <el-form-item label="血常规其他：" prop="" >
                    <el-input v-model="ruleForm2.szbBloodroutineOthers" placeholder="请输入血常规其他"></el-input>
                  </el-form-item>
                  <el-form-item label="尿常规尿蛋白：" prop="szbUrineProtein" >
                    <el-input v-model="ruleForm2.szbUrineProtein" placeholder="请输入尿常规尿蛋白"></el-input>
                  </el-form-item>
                  <el-form-item label="尿常规尿糖：" prop="szbUrineSugar" >
                    <el-input v-model="ruleForm2.szbUrineSugar" placeholder="请输入尿常规尿糖"></el-input>
                  </el-form-item>
                  <el-form-item label="尿常规尿酮体：" prop="szbUrineKetonebody" >
                    <el-input v-model="ruleForm2.szbUrineKetonebody" placeholder="请输入尿常规尿酮体"></el-input>
                  </el-form-item>
                  <el-form-item label="尿常规尿潜血：" prop="szbUrineOccultblood" >
                    <el-input v-model="ruleForm2.szbUrineOccultblood" placeholder="请输入尿常规尿潜血"></el-input>
                  </el-form-item>
                  <el-form-item label="尿常规其他：" prop="szbUrineOthers" >
                    <el-input v-model="ruleForm2.szbUrineOthers" placeholder="请输入尿常规其他"></el-input>
                  </el-form-item>
                  <el-form-item label="血肌酐eGFR:" prop="szbEgfr" >
                    <el-input v-model="ruleForm2.szbEgfr" placeholder="请输入血肌酐eGFR"></el-input>
                  </el-form-item>
                  <!-- <el-form-item label="创建时间:" prop="szbDate" >
                     <el-date-picker
                        v-model="ruleForm2.szbDate"
                        type="date"
                        :disabled="title=='修改' ? true : false "
                        placeholder="选择日期">
                      </el-date-picker>
                  </el-form-item> -->
                  <el-form-item label="肾脏超声" style="display: block;" class="imgStyle" >
                    <el-upload
                        class="upload-demo"
                          action="/threeHignManager/Inteworkst/uploadImg"
                          :on-preview="handlePreview"
                          :on-remove="szRemove"
                          :before-remove="beforeRemove"
                          :on-success="szSuccess"
                          multiple
                          :limit="1"
                          :on-exceed="handleExceed"
                          :file-list="szbFileList">
                          <el-button size="small" :disabled="szLength==1 ? true: false " type="primary">+上传附件</el-button>
                        
                        </el-upload>
                        
                        
                  </el-form-item>  
                  <el-form-item label="诊断说明:" >
                          <el-input v-model="ruleForm2.szbRenalultrasound" ></el-input>
                        </el-form-item>
                  <div class="preservation">
                    <el-button type="primary"  @click="preservation('ruleForm2')">保存</el-button>
                  </div>
                </el-form>
              
              </div>
               <!-- 糖尿病视网膜病变 -->
              <div v-else-if="item.label=='糖尿病视网膜病变' ">
                <el-form  :inline="true" :model="ruleForm3"  ref="ruleForm3"   :rules="rules3">
                  <el-form-item label="视力左：" prop="swmVisionLeft" >
                    <el-input v-model="ruleForm3.swmVisionLeft" placeholder="请输入视力左"></el-input>
                  </el-form-item>
                  <el-form-item label="视力右：" prop="swmVisionRight" >
                    <el-input v-model="ruleForm3.swmVisionRight" placeholder="请输入视力右"></el-input>
                  </el-form-item>
                  <el-form-item label="评估结果：" prop="swmAssessmentResults" >
                    <el-input v-model="ruleForm3.swmAssessmentResults" placeholder="请输入评估结果"></el-input>
                  </el-form-item>
                 
                  <el-form-item label="图像1：" prop="swmImageoneFile" style="display: block;" class="imgStyle" >
                    <el-upload
                        class="upload-demo"
                          action="/threeHignManager/Inteworkst/uploadImg"
                          :on-preview="handlePreview"
                          :on-remove="retinaRemove1"
                          :before-remove="beforeRemove"
                          :on-success="retinaSuccess1"
                          multiple
                          :limit="1"
                          :on-exceed="handleExceed"
                          :file-list="tnbFileList1">
                          <el-button size="small" :disabled="img1Length==1 ? true: false " type="primary">+上传附件</el-button>
                        
                        </el-upload>
                        
                  </el-form-item>  
                  <el-form-item label="诊断说明:" >
                          <el-input v-model="ruleForm3.swmImageone" ></el-input>
                        </el-form-item>

                  <el-form-item label="图像2：" prop="swmImagetwoFile" style="display: block;"  class="imgStyle">
                    <el-upload
                        class="upload-demo"
                          action="/threeHignManager/Inteworkst/uploadImg"
                          :on-preview="handlePreview"
                          :on-remove="retinaRemove2"
                          :before-remove="beforeRemove"
                          :on-success="retinaSuccess2"
                          :on-change="handleChange"
                          multiple
                          :limit="1"
                          :on-exceed="handleExceed"
                          :file-list="tnbFileList2">
                          <el-button size="small" :disabled="img2Length==1 ? true: false "  type="primary">+上传附件</el-button>
                        </el-upload>
                        
                  </el-form-item>
                  <el-form-item label="诊断说明:" >
                    <el-input v-model="ruleForm3.swmImagetwo" ></el-input>
                  </el-form-item>
                  <div class="preservation">
                    <el-button type="primary"  @click="preservation('ruleForm3')">保存</el-button>
                  </div>
                </el-form>
                
              </div>
              <!-- 糖尿病周围神经病变 -->
              <div v-else-if="item.label=='糖尿病周围神经病变' ">
                <el-form :inline="true" :model="ruleForm4"  ref="ruleForm4"   :rules="rules4">
                  <el-form-item label="踝反射：" prop="sjbbAnkleReflex" >
                    <el-input v-model="ruleForm4.sjbbAnkleReflex" placeholder="请输入踝反射"></el-input>
                  </el-form-item>
                  <el-form-item label="针刺痛觉：" prop="sjbbAcupuncturePain" >
                    <el-input v-model="ruleForm4.sjbbAcupuncturePain" placeholder="请输入针刺痛觉"></el-input>
                  </el-form-item>
                  <el-form-item label="震动觉：" prop="sjbbVibrationSense" >
                    <el-input v-model="ruleForm4.sjbbVibrationSense" placeholder="请输入震动觉"></el-input>
                  </el-form-item>

                  <el-form-item label="压力觉：" prop="sjbbPressure" >
                    <el-input v-model="ruleForm4.sjbbPressure" placeholder="请输入压力觉"></el-input>
                  </el-form-item>
                  <el-form-item label="温度觉：" prop="sjbbTemperatureSense" >
                    <el-input v-model="ruleForm4.sjbbTemperatureSense" placeholder="请输入温度觉"></el-input>
                  </el-form-item>
                  <el-form-item label="感觉阈值：" prop="sjbbSensoryThreshold" >
                    <el-input v-model="ruleForm4.sjbbSensoryThreshold" placeholder="请输入感觉阈值"></el-input>
                  </el-form-item>
                  <el-form-item label="神经电生理检查：" prop="sjbbNeuroelectrophysiological" >
                    <el-input v-model="ruleForm4.sjbbNeuroelectrophysiological" placeholder="请输入神经电生理检查"></el-input>
                  </el-form-item>
                  <!-- <el-form-item label="创建时间:" prop="sjbbDate" >
                     <el-date-picker
                        v-model="ruleForm4.sjbbDate"
                        type="date"
                        :disabled="title=='修改' ? true : false "
                        placeholder="选择日期">
                      </el-date-picker>
                  </el-form-item> -->
                  <div class="preservation">
                    <el-button type="primary"  @click="preservation('ruleForm4')">保存</el-button>
                  </div>
                </el-form>
              </div>
              <!-- 糖尿病下肢动脉病变 -->
              <div v-else-if="item.label=='糖尿病下肢动脉病变'">
                <el-form  :inline="true" :model="ruleForm"  ref="ruleForm"   :rules="rules" size="mini">
                  <el-form-item label="皮肤温度：" prop="xzdmSkinTemperature" >
                    <el-input v-model="ruleForm.xzdmSkinTemperature" placeholder="请输入皮肤温度"></el-input>
                  </el-form-item>
                  <el-form-item label="间歇性跛行实验：" prop="xzdmClaudication" >
                    <el-input v-model="ruleForm.xzdmClaudication" placeholder="请输入间歇性跛行实验"></el-input>
                  </el-form-item>
                  <el-form-item label="股动脉杂音：" prop="xzdmArteryMurmur" >
                    <el-input v-model="ruleForm.xzdmArteryMurmur" placeholder="请输入股动脉杂音"></el-input>
                  </el-form-item>
                  <el-form-item label="足背动脉杂音：" prop="xzdmDorsalisPedis" >
                    <el-input v-model="ruleForm.xzdmDorsalisPedis" placeholder="请输入足背动脉杂音"></el-input>
                  </el-form-item>
                  <el-form-item label="踝肱比ABI:" prop="xzdmAbi" >
                    <el-input v-model="ruleForm.xzdmAbi" placeholder="请输入踝肱比ABI"></el-input>
                  </el-form-item>
                  <el-form-item label="左上肢血压：" prop="xzdmLeftupLimb" >
                    <el-input v-model="ruleForm.xzdmLeftupLimb" placeholder="请输入左上肢血压"></el-input>
                  </el-form-item>
                  <el-form-item label="左下肢血压：" prop="xzdmLeftdownLimb" >
                    <el-input v-model="ruleForm.xzdmLeftdownLimb" placeholder="请输入左下肢血压"></el-input>
                  </el-form-item>
                  <el-form-item label="右上肢血压：" prop="xzdmRightupLimb" >
                    <el-input v-model="ruleForm.xzdmRightupLimb" placeholder="请输入右上肢血压"></el-input>
                  </el-form-item>
                  <el-form-item label="右下肢血压：" prop="xzdmRightdownLimb" >
                    <el-input v-model="ruleForm.xzdmRightdownLimb" placeholder="请输入右下肢血压"></el-input>
                  </el-form-item>
                  <el-form-item label="下肢血管超声：" prop="xzdmLowerLimbs" >
                    <el-input v-model="ruleForm.xzdmLowerLimbs" placeholder="请输入下肢血管超声"></el-input>
                  </el-form-item>
                  <el-form-item label="下肢动脉CTA：" prop="xzdmCta" >
                    <el-input v-model="ruleForm.xzdmCta" placeholder="请输入下肢动脉CTA"></el-input>
                  </el-form-item>
                  
                  <div class="preservation">
                    <el-button type="primary"  @click="preservation('ruleForm')">保存</el-button>
                  </div>
                 </el-form>
              </div>
        </el-tab-pane>
      </el-tabs> 
    </el-dialog>
    <!-- 并发症筛查列表 -->
    <el-dialog     title="并发症筛查列表" :visible.sync="dialogVisible2" width="50%" >
      <el-button type="primary" size="small" @click="screen()">新增</el-button>
      <el-table  :data="screenData"  stripe  v-loading='loading2' style="width: 100%">
          <el-table-column prop="name"  label="姓名" width=""> </el-table-column>
          <el-table-column prop="createDate"  label="检查时间" >
           
          </el-table-column>
          <!-- <el-table-column  prop=""  label="状态" width="" align="center" >
            <template slot-scope="scope">
              <div>
                {{scope.row.accomplish=='1' ? '已完成' :  '未完成'}}
              </div>
            </template>
          </el-table-column> -->
          <el-table-column  prop="completionRate"  label="完成率" width="" align="center" > </el-table-column>
          <el-table-column   label="操作" width="80px">
            <template slot-scope="scope">
              <div class="btn">
                <el-button type="success" size="small" @click="amend(scope.row)">修改</el-button>
              </div>
            </template>
          </el-table-column>
        </el-table>
    </el-dialog>

   
  </div>
</template>
<script>
// 
import {selectInteworkst,
getOneVisit,//详情
coronary,//保存   和修改
selectBfz,//并发症查询
selectCoronary,//筛查列表

updatecoronary,//修改保存---
getSession
} from "./../api";
export default {
  data(){
    return{
      title:'',
      particulars:{},//详情 
      pName:'',
      pId:'',
      disease:'',
      screenList:'',
      currentPage:1,
      pageSize:10,
      total:0,
       textarea: '',
      //  fileList:[],
      gxbFilelist1:[],//冠心病
      gxbFilelist2:[],
      fileList: [],//脑卒中
      fileList2:[],
      fileList3: [],
      fileList4: [],
      szbFileList:[],//肾脏病
      tnbFileList1:[],//糖尿病
      tnbFileList2:[],
      name:'',
      input:'',
      radio:1,
      radio1:1,
      radio2:1,
      crowd:'',//人群
      loading:true,
      loading2:true,
      dialogVisible1:false,
      dialogVisible2:false,
      dialogVisible3:false,
      options: [{
          label: '高血压'
        }, {
          label: '糖尿病'
        }, {
          label: '高血脂'
        } ],
        options1: [{
          label: '未完成'
        }, {
          label: '已完成'
        },  ],
       tableData: [ ],
       activeName:0,
       paneData:[],
       illnessName:'',
      // 冠心病
      coronaryDisease:{
        gxbFamily:0,
        gxbAngiography:'',
        gxbKidneyDisease:'',//慢性肾病
        gxbAlb:'',//尿白蛋白
        gxbTroponin:'',//血肌钙蛋白

        gxbCreatineKinase:'',//肌酸激酶
        gxbCta:'',//冠状动脉CTA
        gxbIsoenzyme:'',//肌酸激酶龚同酶
        gxbElectrocardiogramFile:'',//心电图
        gxbElectrocardiogram:'',//心电图说明
        gxbUcgFile:'',//心脏彩超
        gxbUcg:'',//心脏彩超诊断说明
        // gxbDate:'',//创建时间
      },
      //脑卒中
      cerebralApoplexy:{
        nzzHypercholesterolemia:0,// 高胆固醇血症(
         nzzFibrillation:1, //心房颤动
         nzzFartherNzx:1,//家族史父亲有脑卒中
        nzzFartherGxb:1,//家族史父亲有冠心病
        nzzMotherNzx:1,//家族史母亲有脑卒中
        nzzMotherGxb:1,//家族史母亲有冠心病
        nzzBrotherNzx:1,//家族史兄弟姐妹有脑卒中
        nzzBrotherGxb:1,//家族史兄弟姐妹有冠心病
        nzzChildrenNzx:1,//家族史兄子女有脑卒中
        nzzChildrenGxb:1,//家族史子女有冠心病
        nzzSmokingDaily:'',//日吸烟量
        nzzUltrasoundFile:'',//颈动脉超声
        nzzUltrasound:'',// 颈动脉超声诊断说明
        nzzCtFile:'',//颅脑CT
        nzzCt:'',//CT诊断说明
        nzzMriFile:'',//颅脑MRI
        nzzMri:'',//颅脑MRI诊断说明
        nzzMraFile:'',//颅脑MRA
        nzzMra:'',//MRA诊断说明
        // nzzDate:''//创建时间
      },
      //  视网膜
       ruleForm3:{
        swmVisionLeft:'',//视力左
        swmVisionRight:'',
        swmAssessmentResults:'',//评估结果
        swmImageoneFile:'',//图像1
        swmImageone:'',//图像1 说明
        swmImagetwoFile:'',//图像2
        swmImagetwo:'',//图像2
        // swmDate:'',
       },
      //  视网膜病变
       rules3:{
         swmVisionLeft:[
           { required: true, message: '请输入视力左', trigger: 'blur' }
         ],
         swmVisionRight:[
           { required: true, message: '请输入视力左', trigger: 'blur' }
         ],
         swmAssessmentResults:[
          { required: true, message: '请输入评估结果', trigger: 'blur' }
         ],
        //  swmImageoneFile:[{ required: true, message: '请上传图像', trigger: 'blur' }],
        //  swmImagetwoFile:[{ required: true, message: '请上传图像', trigger: 'blur' }],
        // swmDate:[
        //   { required: true, message: '请输入创建时间', trigger: 'blur' }
        //  ],
       },
      //  肾脏病
       ruleForm2:{
         //  肾脏病
        //  redProtein:'',
         szbHemoglobin:'',//血常规血红蛋白
         szbHemameba:'',//血常规白细胞

         szbPlatelet:'',//血常规血小板
         szbBloodroutineOthers:'',//血常规其他
         szbUrineProtein:'',//尿常规尿蛋白
         szbUrineSugar:'',//尿常规尿糖
         szbUrineKetonebody:'',//尿常规尿酮体
         szbUrineOccultblood:'',//尿常规尿潜血

         szbUrineOthers:'',//尿常规其他
         szbEgfr:'',//血肌酐eGFR
         szbRenalultrasoundFile:'',//肾脏超声
         szbRenalultrasound:'',//肾脏超声诊断说明
        //  szbDate:'',//创建时间
       },
      //  肾脏病
       rules2:{
         szbHemoglobin:[
           { required: true, message: '请输入血常规血红蛋白', trigger: 'blur' }
         ],
         szbHemameba:[
           { required: true, message: '请输入血常规白细胞', trigger: 'blur' }
         ],
         szbPlatelet:[{ required: true, message: '请输入血常规血小板', trigger: 'blur' }],
         szbUrineProtein:[{ required: true, message: '请输入尿常规尿蛋白', trigger: 'blur' }],
         szbUrineSugar:[{ required: true, message: '请输入尿常规尿糖', trigger: 'blur' }],
         szbUrineKetonebody:[{ required: true, message: '请输入尿常规尿酮体', trigger: 'blur' }],
         szbUrineOccultblood:[{ required: true, message: '请输入尿常规尿潜血', trigger: 'blur' }],
         szbUrineOthers:[{ required: true, message: '请输入尿常规其他', trigger: 'blur' }],
         szbEgfr:[{ required: true, message: '请输入血肌酐eGFR', trigger: 'blur' },{ pattern:/^1[6-9]$|^[2-9]\d$|^1[0-3][0-3]$/, message: '范围在44-133', trigger: 'blur'     }]

        //  szbDate:[{required: true, message: '请输入创建时间', trigger: 'blur'}] 
       },
        // 糖尿病周围神经病变
       ruleForm4:{
         
        sjbbAnkleReflex:'',//踝反射
        sjbbAcupuncturePain: '' ,//针刺痛觉
        sjbbVibrationSense:'',//震动觉
        sjbbPressure: '' ,//压力觉
        sjbbTemperatureSense :' ' ,//温度觉
        sjbbSensoryThreshold: '',//感觉阈值
        sjbbNeuroelectrophysiological: '' ,//神经电生理检查
        // sjbbDate:'',
       } ,
       //糖尿病周围神经病变
       rules4:{
         
        sjbbAnkleReflex: [{ required: true, message: '请输入踝反射', trigger: 'blur' }] ,
        sjbbAcupuncturePain: [{ required: true, message: '请输入针刺痛觉', trigger: 'blur' }] ,
        sjbbVibrationSense: [{ required: true, message: '请输入震动觉', trigger: 'blur' }] ,
        sjbbPressure: [{ required: true, message: '请输入压力觉', trigger: 'blur' }] ,
        sjbbTemperatureSense: [{ required: true, message: '请输入温度觉', trigger: 'blur' }] ,
        sjbbSensoryThreshold: [{ required: true, message: '请输入感觉阈值', trigger: 'blur' }] ,
        sjbbNeuroelectrophysiological: [{ required: true, message: '请输入神经电生理检查', trigger: 'blur' }] ,
        // sjbbDate: [{ required: true, message: '请输创建时间', trigger: 'blur' }] 
       },
         //动脉病变
        ruleForm:{
        xzdmSkinTemperature:'',//皮肤温度
        xzdmClaudication:'',//间歇性跛行实验
        xzdmArteryMurmur:'',//股动脉杂音
        xzdmDorsalisPedis:'',//足背动脉杂音
        xzdmAbi:'',//踝肱比ABI
        xzdmLeftupLimb:'',//左上肢血压
        xzdmLeftdownLimb:'',//左下
        xzdmRightupLimb:'',//右上
        xzdmRightdownLimb:'',//右下
        xzdmLowerLimbs:'',//下肢血管超声
        xzdmCta:'',//下肢动脉CTA
        // xzdmDate:'',
       },
       rules:{
        //动脉   
        xzdmSkinTemperature:[{ required: true, message: '请输入皮肤温度', trigger: 'blur' }] ,
        xzdmClaudication:[{ required: true, message: '请输入间歇性跛行实验', trigger: 'blur' }] ,
        xzdmArteryMurmur:[{ required: true, message: '请输入股动脉杂音', trigger: 'blur' }],
        xzdmDorsalisPedis:[{ required: true, message: '请输入足背动脉杂音', trigger: 'blur' }],
        xzdmAbi:[{ required: true, message: '请输入踝肱比ABI', trigger: 'blur' }],
        xzdmLeftupLimb:[{ required: true, message: '请输入左上肢血压', trigger: 'blur' }],
        xzdmLeftdownLimb:[{ required: true, message: '请输入左下肢血压', trigger: 'blur' }],
        xzdmRightupLimb:[{ required: true, message: '请输入右上肢血压', trigger: 'blur' }],
        xzdmRightdownLimb:[{ required: true, message: '请输入右下肢血压', trigger: 'blur' }],
        xzdmLowerLimbs:[{ required: true, message: '请输入下肢血管超声', trigger: 'blur' }],
        xzdmCta:[{ required: true, message: '请输入下肢动脉CTA', trigger: 'blur' }],
        // xzdmDate:[{ required: true, message: '请输创建时间', trigger: 'blur' }] 
       },
       userRole:'',
       location:'',
       shortId:'',
       bfzName:'冠心病',
      dCode:'',
      electrocardiogram:[],//心电图上传文件
      gxbUcgFile:[],//彩超
      MRI:[],
      MRA:[],
      createDate:'',//检查时间
      screenData:[],//筛查列表
      modifyList:{},
      fileLength:0,
      xzccLength:0,
      jdmLength:0,//颈动脉超声
      ctLength:0,//颅脑CT
      MRILength:0,//MRI
      MRALength:0,//MRA
      szLength:0,//肾脏病
      img1Length:0,//视网膜
      img2Length:0,
      xdtList:[]
    }
  },
  mounted(){
    // this.location=sessionStorage.getItem('location')
    // this.userRole=sessionStorage.getItem('userRole')
    getSession().then(result=>{
      if(result.success==true){
        this.location=result.data.region.rcode
        this.userRole=result.data.region.rgrade
        this.query()
      }
    })
    
    this.screenList='未完成'
  },
  methods:{
    
    handleChange(file, fileList) {
     
      },
    //并发症筛查列表
    screeningList(val){
      if(val.ipType){
        this.illnessName =val.ipType
        if(this.illnessName =='糖尿病'){
          this.paneData=[
            {label:'冠心病'},
            {label:'脑卒中'},
            {label:'糖尿病肾病'},
            {label:'糖尿病视网膜病变'},
            {label:'糖尿病周围神经病变'},
            {label:'糖尿病下肢动脉病变'},
          ] 
        }
        if(this.illnessName =='高血压'|| this.illnessName =='高血脂'){
          this.paneData=[
            {label:'冠心病'},
            {label:'脑卒中'},
            {label:'肾脏病'},
          ]
        }
      }
      
      this.shortId=val.id
      this.dCode=val.dCode
      
      this.dialogVisible2=true
      let params={
        dcode:val.dCode,   
        sid:val.id
      }
      this.screenData=[]
      selectCoronary(params).then(result=>{
        this.loading2=false
        if(result.status==true){
          if(result.data.length!==0){
            this.screenData=result.data
          }
         
        }else{
          this.$message.error(result.message)
        }
      })

    }, 
    // 保存
    preservation(formName){

      // 脑卒中  冠心病
      if(this.bfzName=='冠心病' || this.bfzName=='脑卒中'){
         let params={}
        if(this.bfzName=='冠心病'){
          var file=[]
          var arr={}
          console.log('&&&&&&',this.xdtList)
          if(this.xdtList.length==0){
            this.coronaryDisease.gxbElectrocardiogramFile=''
          }else{
            this.coronaryDisease.gxbElectrocardiogramFile=this.xdtList[0].name + ',' + this.xdtList[0].response.data
          }
          // if(this.electrocardiogram.length==0){
          //   this.coronaryDisease.gxbElectrocardiogramFile=''
          // }else{
          //   this.coronaryDisease.gxbElectrocardiogramFile=this.electrocardiogram[0].name + ','+this.electrocardiogram[0].response.data
          // }
          var gxbUcgFile=[]
          if(this.gxbUcgFile.length==0){
            this.coronaryDisease.gxbUcgFile=''
          }else{
            this.coronaryDisease.gxbUcgFile=this.gxbUcgFile[0].name + ','+this.gxbUcgFile[0].response.data
          }
          params={
            gxbFamily:this.coronaryDisease.gxbFamily,
            gxbAngiography:this.coronaryDisease.gxbAngiography,
            gxbKidneyDisease:this.coronaryDisease.gxbKidneyDisease,//慢性肾病
            gxbAlb:this.coronaryDisease.gxbAlb,//尿白蛋白
            gxbTroponin:this.coronaryDisease.gxbTroponin,//血肌钙蛋白
            gxbCreatineKinase:this.coronaryDisease.gxbCreatineKinase,//肌酸激酶
            gxbCta:this.coronaryDisease.gxbCta,//冠状动脉CTA
            gxbIsoenzyme:this.coronaryDisease.gxbIsoenzyme,//肌酸激酶龚同酶
            // gxbElectrocardiogramFile:this.coronaryDisease.gxbElectrocardiogramFile,//心电图
            gxbElectrocardiogramUp:this.coronaryDisease.gxbElectrocardiogramFile,
            gxbElectrocardiogram:this.coronaryDisease.gxbElectrocardiogram,//心电图说明
            // gxbUcgFile:this.coronaryDisease.gxbUcgFile,//心脏彩超
            gxbUcgUp:this.coronaryDisease.gxbUcgFile,
            gxbUcg:this.coronaryDisease.gxbUcg,//心脏彩超诊断说明
            // gxbDate:this.coronaryDisease.gxbDate//创建时间
          }
        }
        if(this.bfzName=='脑卒中'){
          var file=[]
          if(this.electrocardiogram.length==0){
            this.cerebralApoplexy.nzzUltrasoundFile=''
          }else{
            this.cerebralApoplexy.nzzUltrasoundFile=this.electrocardiogram[0].name + ','+this.electrocardiogram[0].response.data
            
          }
          
          var gxbUcgFile=[]
          console.log(2222,this.gxbUcgFile)
          if(this.gxbUcgFile.length==0){
            this.cerebralApoplexy.nzzCtFile=''
          }else{
            this.cerebralApoplexy.nzzCtFile=this.gxbUcgFile[0].name + ','+this.gxbUcgFile[0].response.data
           
          }
          
          var nzzMriFile=[]
          if(this.MRI.length==0){
            this.cerebralApoplexy.nzzMriFile==''
          }else{
            this.cerebralApoplexy.nzzMriFile=this.MRI[0].name + ','+this.MRI[0].response.data
           
          }
          
          var nzzMraFile=[]
          if(this.MRA.length==0){
            this.cerebralApoplexy.nzzMraFile=''
          }else{
            this.cerebralApoplexy.nzzMraFile=this.MRA[0].name + ','+this.MRA[0].response.data
            
          }
          
          params={
            nzzHypercholesterolemia:this.cerebralApoplexy.nzzHypercholesterolemia,// 高胆固醇血症(
            nzzFibrillation:this.cerebralApoplexy.nzzFibrillation, //心房颤动
            nzzFartherNzx:this.cerebralApoplexy.nzzFartherNzx,//家族史父亲有脑卒中
            nzzFartherGxb:this.cerebralApoplexy.nzzFartherGxb,//家族史父亲有冠心病
            nzzMotherNzx:this.cerebralApoplexy.nzzMotherNzx,//家族史母亲有脑卒中
            nzzMotherGxb:this.cerebralApoplexy.nzzMotherGxb,//家族史母亲有冠心病
            nzzBrotherNzx:this.cerebralApoplexy.nzzBrotherNzx,//家族史兄弟姐妹有脑卒中
            nzzBrotherGxb:this.cerebralApoplexy.nzzBrotherGxb,//家族史兄弟姐妹有冠心病
            nzzChildrenNzx:this.cerebralApoplexy.nzzChildrenNzx,//家族史兄子女有脑卒中
            nzzChildrenGxb:this.cerebralApoplexy.nzzChildrenGxb,//家族史子女有冠心病
            nzzSmokingDaily:this.cerebralApoplexy.nzzSmokingDaily,//日吸烟量
            // nzzUltrasoundFile:this.cerebralApoplexy.nzzUltrasoundFile,//颈动脉超声
            nzzUltrasoundUp:this.cerebralApoplexy.nzzUltrasoundFile,
            nzzUltrasound:this.cerebralApoplexy.nzzUltrasound,// 颈动脉超声诊断说明
            // nzzCtFile:this.cerebralApoplexy.nzzCtFile,//颅脑CT
            nzzCtUp:this.cerebralApoplexy.nzzCtFile,//颅脑CT
            nzzCt:this.cerebralApoplexy.nzzCt,//CT诊断说明
            // nzzMriFile:this.cerebralApoplexy.nzzMriFile,//颅脑MRI
            nzzMriUp:this.cerebralApoplexy.nzzMriFile,//颅脑MRI
            nzzMri:this.cerebralApoplexy.nzzMri,//颅脑MRI诊断说明
            // nzzMraFile:this.cerebralApoplexy.nzzMraFile,//颅脑MRA
            nzzMraUp:this.cerebralApoplexy.nzzMraFile,//颅脑MRA
            nzzMra:this.cerebralApoplexy.nzzMra,//MRA诊断说明
            // nzzDate:this.cerebralApoplexy.nzzDate
          }
        }
        
        if(this.title=='新增'){
          if(!this.createDate){
              this.$message.error('请选择检查时间！')
              return
          }
          params.bfzName=this.bfzName
          params.shortId=this.shortId
          params.diseaseCode=this.dCode
          var time=  this.$moment(this.createDate).format("YYYY-MM-DD")
          params.createDate=time
          console.log('参数1',params)
          coronary(params).then(result=>{
            if(result.status==true){
              var list ={
                id: this.shortId,
                dCode:this.dCode
              }
              this.screeningList(list)
              this.query()
              this.dialogVisible3=false
              this.$message.success(result.data)
              //清空上传附件
              this.gxbFilelist1=[];
              this.gxbFilelist2=[];
              this.szbFileList=[];
              this.tnbFileList1=[];
              this.tnbFileList2=[];
              this.fileList=[]
              this.fileList2=[]
              this.fileList3=[]
              this.fileList4=[]
              this.electrocardiogram=[]
              this.gxbUcgFile=[]
              this.MRI=[]
              this.MRA=[]
            }else{
              this.$message.error(result.message)
            }
          })
        }
        if(this.title=='修改'){
          params.shortId=this.modifyList.shortId
          params.diseaseCode=this.modifyList.dcode
          params.id=this.modifyList.id
          console.log('参数2',params)
          updatecoronary(params).then(result=>{
            if(result.status==true){
              var list ={
                    id: this.shortId,
                    dCode:this.dCode
                  }
                  this.screeningList(list)
                  this.query()
              this.dialogVisible3=false
              this.$message.success(result.data)
               this.electrocardiogram=[]
              this.gxbUcgFile=[]
              this.MRI=[]
              this.MRA=[]
              this.fileLength=0;
              this.xzccLength=0;
              this.jdmLength=0;//颈动脉超声
              this.ctLength=0;//颅脑CT
              this.MRILength=0;//MRI
              this.MRALength=0;//MRA
             
            }else{
              this.$message.error(result.message)
            }
          })
        }
        
        
      }else{
        // 糖尿病肾病 糖尿病视网膜病变  糖尿病周围神经病变  糖尿病下肢动脉病变   （验证）
        this.$refs[formName][0].validate(valid=>{
          if(valid==true){
            let params={}
            
            if(this.bfzName=='糖尿病肾病' || this.bfzName=='肾脏病'){
              var file=[]
              
              if(this.electrocardiogram.length==0){
                this.ruleForm2.szbRenalultrasoundFile=''
              }else{
                this.ruleForm2.szbRenalultrasoundFile=this.electrocardiogram[0].name + ',' +this.electrocardiogram[0].response.data
                // this.electrocardiogram.forEach(item=>{
                //   file.push(item.response.data)
                //   this.ruleForm2.szbRenalultrasoundFile=file.toString()
                // })
              }
              params={
                szbHemoglobin:this.ruleForm2.szbHemoglobin,//血常规血红蛋白
                szbHemameba:this.ruleForm2.szbHemameba,//血常规白细胞
                szbPlatelet:this.ruleForm2.szbPlatelet,//血常规血小板
                szbBloodroutineOthers:this.ruleForm2.szbBloodroutineOthers,//血常规其他
                szbUrineProtein:this.ruleForm2.szbUrineProtein,//尿常规尿蛋白
                szbUrineSugar:this.ruleForm2.szbUrineSugar,//尿常规尿糖
                szbUrineKetonebody:this.ruleForm2.szbUrineKetonebody,//尿常规尿酮体
                szbUrineOccultblood:this.ruleForm2.szbUrineOccultblood,//尿常规尿潜血
                szbUrineOthers:this.ruleForm2.szbUrineOthers,//尿常规其他
                szbEgfr:this.ruleForm2.szbEgfr,//血肌酐eGFR
                // szbRenalultrasoundFile:this.ruleForm2.szbRenalultrasoundFile,//肾脏超声
                szbRenalultrasoundUp:this.ruleForm2.szbRenalultrasoundFile,
                szbRenalultrasound:this.ruleForm2.szbRenalultrasound,//肾脏超声诊断说明
                // szbDate:this.ruleForm2.szbDate
              }
            }
            if(this.bfzName=='糖尿病视网膜病变'){
              var file=[]
              if(this.electrocardiogram.length==0){
                this.ruleForm3.swmImageoneFile=''  
              }else{
                this.electrocardiogram.forEach(item=>{
                  file.push(item.response.data)
                  this.ruleForm3.swmImageoneFile=file.toString()
                })
              }
              
              var gxbUcgFile=[]
              if(this.gxbUcgFile.length==0){
                this.ruleForm3.swmImagetwoFile=''
              }else{
                this.gxbUcgFile.forEach(item=>{
                  gxbUcgFile.push(item.response.data)
                  this.ruleForm3.swmImagetwoFile=gxbUcgFile.toString()
                })
              }
              
              params={
                swmVisionLeft:this.ruleForm3.swmVisionLeft,
                swmVisionRight:this.ruleForm3.swmVisionRight,
                swmAssessmentResults:this.ruleForm3.swmAssessmentResults,
                // swmImageoneFile:this.ruleForm3.swmImageoneFile,
                swmImageoneUp:this.ruleForm3.swmImageoneFile,
                swmImageone:this.ruleForm3.swmImageone,
                // swmImagetwoFile:this.ruleForm3.swmImagetwoFile,
                swmImagetwoUp:this.ruleForm3.swmImagetwoFile,
                swmImagetwo:this.ruleForm3.swmImagetwo,
                // swmDate:this.ruleForm3.swmDate
              }
            }
            if(this.bfzName=='糖尿病周围神经病变'){
              params={
                sjbbAnkleReflex:this.ruleForm4.sjbbAnkleReflex,//踝反射
                sjbbAcupuncturePain: this.ruleForm4.sjbbAcupuncturePain ,//针刺痛觉
                sjbbVibrationSense:this.ruleForm4.sjbbVibrationSense,//震动觉
                sjbbPressure: this.ruleForm4.sjbbPressure ,//压力觉
                sjbbTemperatureSense :this.ruleForm4.sjbbTemperatureSense ,//温度觉
                sjbbSensoryThreshold: this.ruleForm4.sjbbSensoryThreshold,//感觉阈值
                sjbbNeuroelectrophysiological: this.ruleForm4.sjbbNeuroelectrophysiological ,//神经电生理检查
                // sjbbDate:this.ruleForm4.sjbbDate
              }
            }
            if(this.bfzName=='糖尿病下肢动脉病变'){
              params={
                xzdmSkinTemperature:this.ruleForm.xzdmSkinTemperature,//皮肤温度
                xzdmClaudication:this.ruleForm.xzdmClaudication,//间歇性跛行实验
                xzdmArteryMurmur:this.ruleForm.xzdmArteryMurmur,//股动脉杂音
                xzdmDorsalisPedis:this.ruleForm.xzdmDorsalisPedis,//足背动脉杂音
                xzdmAbi:this.ruleForm.xzdmAbi,//踝肱比ABI
                xzdmLeftupLimb:this.ruleForm.xzdmLeftupLimb,//左上肢血压
                xzdmLeftdownLimb:this.ruleForm.xzdmLeftdownLimb,//左下
                xzdmRightupLimb:this.ruleForm.xzdmRightupLimb,//右上
                xzdmRightdownLimb:this.ruleForm.xzdmRightdownLimb,//右下
                xzdmLowerLimbs:this.ruleForm.xzdmLowerLimbs,//下肢血管超声
                xzdmCta:this.ruleForm.xzdmCta,//下肢动脉CTA
                // xzdmDate:this.ruleForm.xzdmDate
              }
            }
            
            if(this.title=='新增'){
              if(!this.createDate){
                this.$message.error('请选择检查时间！')
                return
              }
              params.bfzName=this.bfzName
              params.shortId=this.shortId
              params.diseaseCode=this.dCode
              // params.createDate=this.createDate
              var y = this.createDate.getFullYear();
              var m = this.createDate.getMonth()+1<10 ? '0' + (this.createDate.getMonth()+1) :this.createDate.getMonth()+1;
              var d = this.createDate.getDate() <10 ? '0'+ this.createDate.getDate() :this.createDate.getDate() <10;
              var time=y + '-' + m + '-' + d
              params.createDate=time
              coronary(params).then(result=>{
                if(result.status==true){
                  // 新增成功 更新筛查列表
                  var list ={
                    id: this.shortId,
                    dCode:this.dCode
                  }
                  this.screeningList(list)
                  this.query()
                  this.dialogVisible3=false
                  this.$message.success(result.data)
                  this.gxbFilelist1=[];
                  this.gxbFilelist2=[];
                  this.szbFileList=[];
                  this.tnbFileList1=[];
                  this.tnbFileList2=[];
                  this.fileList=[]
                  this.fileList2=[]
                  this.fileList3=[]
                  this.fileList4=[]
                  this.electrocardiogram=[]
                  this.gxbUcgFile=[]
                  this.MRI=[]
                  this.MRA=[]
                }else{
                  this.$message.error(result.message)
                }
              })
            }
            if(this.title=='修改'){
              params.shortId=this.modifyList.shortId
              params.diseaseCode=this.modifyList.dcode
              params.id=this.modifyList.id
              console.log('yanzeng',params)
              updatecoronary(params).then(result=>{
                if(result.status==true){
                  var list ={
                    id: this.shortId,
                    dCode:this.dCode
                  }
                  this.screeningList(list)
                  this.query()
                  this.dialogVisible3=false
                  this.$message.success(result.data)
                  this.electrocardiogram=[]
                  this.gxbUcgFile=[]
                  this.MRI=[]
                  this.MRA=[]

                  this.szLength=0;//肾脏病
                  this.img1Length=0;//视网膜
                  this.img2Length=0;
                }else{
                  this.$message.error(result.message)
                }
              })
            }
            
          }
        })
      }
    },
    // 心电图 成功 
    xdtSuccess(response, file, fileList){
      this.fileLength=fileList.length
      if(this.title=='修改'){
        fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
        console.log('22222',fileList)
      }
      this.xdtList=fileList
      console.log('xinzeng',fileList)
    },
    // 移除
    xdtRemove(file, fileList){
      this.fileLength=fileList.length
       if(this.title=='修改'){
         fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
      } 
      this.xstList=fileList
      // this.electrocardiogram=fileList
      console.log(this.electrocardiogram)
    },
    // 心脏彩超
    xzccSuccess(response, file, fileList) {
      this.xzccLength=fileList.length
      if(this.title=='修改'){
        fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
      }
      this.gxbUcgFile=fileList
    },
    // 
    xzccRemove(file, fileList) {
      this.xzccLength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
       this.gxbUcgFile=fileList
      },
    // 脑卒中
      //颈动脉超声： 移除 
     handleRemove(file, fileList) {
       this.jdmLength=fileList.length
       if(this.title=='修改'){
         fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
       } 
        this.electrocardiogram=fileList
        console.log('脑',this.electrocardiogram)
      },
      // 上传成功
      handleAvatarSuccess(response, file, fileList) {
         this.jdmLength=fileList.length
          if(this.title=='修改'){
            
            fileList.forEach(item=>{
              item.response={
                data:item.url
              }
            })
          }
          this.electrocardiogram=fileList
          console.log('脑',this.electrocardiogram)
      },
      // 
      handleRemove2(file, fileList) {
        this.ctLength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
       this.gxbUcgFile=fileList
      },
      //上传成功
      handleAvatarSuccess2(response, file, fileList) {
        this.ctLength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
        this.gxbUcgFile=fileList
      },
      // 
      handleRemove3(file, fileList) {
        this.MRILength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
       this.MRI=fileList
      },
      //
      handleAvatarSuccess3(response, file, fileList) {
        this.MRILength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
        this.MRI=fileList
      },
      // 
      handleRemove4(file, fileList) {
        this.MRALength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
       this.MRA=fileList
      },
      //
      handleAvatarSuccess4(response, file, fileList) {
        this.MRALength=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
        this.MRA=fileList
      },

      //
       // 肾脏病
      szSuccess(response, file, fileList){
         this.szLength=fileList.length
          if(this.title=='修改'){
            fileList.forEach(item=>{
              item.response={
                data:item.url
              }
            })
          }
          this.electrocardiogram=fileList
      },
      szRemove(file, fileList) {
       this.szLength=fileList.length
       if(this.title=='修改'){
         fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
       } 
        this.electrocardiogram=fileList
      },
      // 视网膜
      retinaSuccess1(response, file, fileList){
         this.img1Length=fileList.length
          if(this.title=='修改'){
            fileList.forEach(item=>{
              item.response={
                data:item.url
              }
            })
          }
          this.electrocardiogram=fileList
      },
      retinaRemove1(file, fileList){
        this.img1Length=fileList.length
       if(this.title=='修改'){
         fileList.forEach(item=>{
          item.response={
            data:item.url
          }
        })
       } 
        this.electrocardiogram=fileList
      }, 

       retinaRemove2(file, fileList) {
         this.img2Length=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
       this.gxbUcgFile=fileList
      },
      retinaSuccess2(response, file, fileList) {
        this.img2Length=fileList.length
        if(this.title=='修改'){
          fileList.forEach(item=>{
            item.response={
              data:item.url
            }
          })
        }
        this.gxbUcgFile=fileList
      },


      handlePreview(file,fileList) {
        console.log('1111',file,fileList)
      },



      handleExceed1(files, fileList) {
        console.log('限制上传文件个数',files, fileList)
        this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      handleExceed(files, fileList) {
        console.log('限制上传文件个数',files, fileList)
        this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      beforeRemove(file, fileList) {
        return this.$confirm(`确定移除 ${ file.name }？`);
      },
    // 查询
    query(){
      let params={
        pName:this.pName,
        pId:this.pId,
        disease:this.disease,
        page: this.currentPage,
        pageSize:this.pageSize,
        location:this.location,
        rgrade:this.userRole,
        accomplish:this.screenList=='已完成' ? '1'  : '0'
      }
      selectInteworkst(params).then(result=>{
        this.loading=false
        if(result.success==true){
          this.tableData=result.data.list
          this.total=result.data.total
        }else{
          this.$message.error(result.data);
        }
      })
    },
    queryUser(){
      this.query()
      
    },
    details(item){
      this.dialogVisible1=true
      let params={
        id:'',
        sid:item.id
      }
      getOneVisit(params).then(result=>{
        if(result.data==null){
          this.$message.error(result.message)
        }else{
          this.particulars=result.data
        }
        
      })
    },
    handleClick(tab, event){
      this.bfzName='冠心病'
      this.bfzName=tab.label
    },
    // 修改
    amend(item){
      this.modifyList=item
      this.title='修改'
      this.coronaryDisease={}
      this.cerebralApoplexy={}//脑卒中
      this.ruleForm2={}//肾脏病
      this.ruleForm3={}//视网膜病变
      this.ruleForm4={} //神经病变
      this.ruleForm={}//下肢动脉病变
      let params={
        id:item.id,
      }
      selectBfz(params).then(result=>{
        if(result.data!==null){
          var res=result.data
          // this.fileList
          // 回显图片
            // 冠心病
            console.log('回显',res)
            if(res.gxbElectrocardiogramImage){
              this.fileLength=1
              this.gxbFilelist1=res.gxbElectrocardiogramImage
            }
            if(res.gxbUcgImage){
              this.xzccLength=1
              this.gxbFilelist2=res.gxbUcgImage
            }
            // 脑卒中
            if(res.nzzUltrasoundImage){
               this.jdmLength=1
              this.fileList=res.nzzUltrasoundImage//颈动脉超声：
            }
            if(res.nzzCtImage){
              this.ctLength=1
              this.fileList2=res.nzzCtImage//颅脑CT
            }
            if(res.nzzMriImage){
              this.MRILength=1
              this.fileList3=res.nzzMriImage//颅脑MRI：
            }
            if(res.nzzMraImage){
              this.MRALength=1
              this.fileList4=res.nzzMraImage  //颅脑MRA
            } 
            // 肾脏病
            if(res.szbRenalultrasoundImage){
              this.szLength=1
              this.szbFileList=res.szbRenalultrasoundImage
            }
            // 糖尿病
            if(res.swmImageoneImage){
              this.img1Length=1
              this.tnbFileList1=res.swmImageoneImage
            }
            if(res.swmImagetwoImage){
              this.img2Length=1
              this.tnbFileList2=res.swmImagetwoImage
            }
         
          this.coronaryDisease=result.data
          this.cerebralApoplexy=result.data//脑卒中
          this.ruleForm2=result.data//肾脏病
          this.ruleForm3=result.data//视网膜病变
          this.ruleForm4=result.data //神经病变
          this.ruleForm=result.data//下肢动脉病变
        }
      })
     
       this.dialogVisible3=true
    },
    //并发症筛查
    screen(){
      this.title='新增'
      this.coronaryDisease={}
      this.cerebralApoplexy={}//脑卒中
      this.ruleForm2={}//肾脏病
      this.ruleForm3={}//视网膜病变
      this.ruleForm4={} //神经病变
      this.ruleForm={}//下肢动脉病变
      this.fileLength=0,
      this.xzccLength=0,
      this.jdmLength=0,//颈动脉超声
      this.ctLength=0,//颅脑CT
      this.MRILength=0,//MRI
      this.MRALength=0,//MRA
      this.szLength=0,//肾脏病
      this.img1Leng=0,//视网膜
      this.img2Length=0,
      
       this.dialogVisible3=true
    },
    handleDialogClose(){
      this.dialogVisible3=false
      this.activeName='0'
      this.createDate='',//检查时间
      //清空上传附件
      this.gxbFilelist1=[]
      this.gxbFilelist2=[]
      this.tnbFileList1=[]
      this.tnbFileList2=[]
      this.szbFileList=[]
      this.fileList=[]
      this.fileList2=[]
      this.fileList3=[]
      this.fileList4=[]
    },
    // fenye
    handleSizeChange(val) {
      this.pageSize = val;
      this.query()
    },
    handleCurrentChange(val) {
      this.currentPage = val;
      this.query()
    },
  },
  watch:{
    
    $route(){
      this.location=this.$route.query.rcode;
      this.userRole= this.$route.query.rgrade;
      this.query()
    },
   
  },
}
</script>
<style scoped>
.box{
  margin-bottom:30px ;
}
.dialogSty li{
  display: flex;
 
}
.dialogSty li div{
  padding: 4px 0;
  font-size: 16px;
}
.dialogSty li div p{
  display: inline-block;
}
.dialogSty li div:nth-child(1){
  width: 45%;
  /* background: red; */
}
.dialogSty li div:nth-child(1) div{
   /* background: blue; */
   width: 48%;
}
.dialogSty li div:nth-child(1) p{
  width: 37%;
}
.dialogSty li >div:nth-child(2){
  width: 55%;
  /* background: red; */
  /* width: 42%; */
}
.dialogSty li >div:nth-child(2) div{
  width: 50%;
}

.dialogSty li div:nth-child(2) p{
  /* width: 42%; */
  width: 32%;
}
.stroke  li div{
  /* background: red; */
  width: 50% !important;

}
.stroke li div p{
  vertical-align: middle;
  /* background: red; */
}
.stroke li div .el-radio-group{
  /* width: 35%;
  background: blue; */
}
.dialogItem  .el-form-item{
  width: 30%;
}
.el-upload--text{
  border: none;
  width: 70px !important;
  height: 30px !important;
}


.imgStyle >>>.el-upload--text{
   border: none;
  width: 70px !important;
  height: 30px !important;
}
.preservation{
  width: 100%;
  text-align: center;
  margin-top: 10px;
  /* margin: auto; */
}
.preservation button{
  width: 40%;
  padding: 15px;
}
#xzSty >>>.el-form-item {
 
  width: 100% !important;
}
#xzSty>>>.el-form-item__content {
  width: 80%;
}
#xzSty>>>.el-form-item__label{
  width: 80px;
}
.el-textarea .el-textarea__inner{
  resize: none;
}


/* .btn .el-button{
  background: none;
}
.btn .el-button--primary {
  background: none  !important;
  border: 1px #318aff solid !important;
}
.btn .el-button:first-child{
 color: #ff4949
}
  .btn .el-button:nth-child(2){
 color: #13ce66
}
  .btn .el-button:nth-child(3){
 color: #318aff
}
.btn .el-button:nth-child(4){
  color: #f7ba2a;
} */

#xqdialog>>>.el-dialog__title {
        color: #409eff;
    }

    ul,
    li {
        list-style: none;
    }

    .helper_tablist1 {
        border: 1px solid #E5E5E5;
        width: 100%;
        height: 87.5%;
    }

    .helper_tablist1 li {
        width: 100%;
    }

    .helper_tablist1 li span:nth-of-type(1) {
        padding: 0 0 4% 0;
        display: inline-block;
        width: 47%;
        vertical-align: top;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        text-align: left;
        font-size: 14px;
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
    }

    .helper_tablist1 li span:nth-of-type(2) {
        padding: 0 0 4% 0;
        display: inline-block;
        width: 47%;
        vertical-align: top;
        -webkit-box-sizing: border-box;
        box-sizing: border-box;
        text-align: left;
        font-size: 14px;

    }
    .slash{
      font-size: 30px;  vertical-align: middle;
    }

    .tabItme .el-form-item{
      width: 40%;
      /* background: red; */
    }

    .diseaseSty span{
      display: inline-block;
      padding-right: 10px;
      /* display: block; */
    }
    .box{
      margin-bottom: 70px;
    }
</style>