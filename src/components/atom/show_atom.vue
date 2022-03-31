<template>
  <div> <el-descriptions class="margin-top" title="原子服务展示信息" :column="3" ></el-descriptions>
  <el-card   >

      <div v-for="atom in atoms" :key="atom.id"  style="width: 1000px;margin-left: 200px" >
        <el-card>
          <label>{{atom.name}}</label>
        <br>
        <br>
        <label style="font-size:16px ;left: 20px"> {{atom.detail}}</label>
        <br>
        <br>
        <el-switch
          style="display: block;"
          v-model="atom.choice"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-text="使用"
          inactive-text="不使用"
          @change='changeStatus(atom)'
        >
        </el-switch>
        <br>
        <el-checkbox-group v-model="atom.allow" @change="change(atom)" >
          <el-checkbox-button v-for="element in atom.params.constraints" :label="element" :key="element">{{element}}</el-checkbox-button>
        </el-checkbox-group>
        </el-card>
        <br><br>
      </div>


  </el-card></div>
</template>

<script>
export default {
  name: "show_atom",
  data() {
    return {
      activeName: '1',
      list: [],
      idNum: 0,
      atoms:[],
    };
  },
  methods: {
    changeStatus(data){
      if(data.choice){
        this.list.push(data)
      }
      else {

        for (let i=0; i<this.list.length; i++){
          if (this.list[i].id === data.id){
            this.list.splice(i, 1)
          }
        }
      }
    },
    get(){
      this.$axios.get('/product/allServices',{headers:{Authorization:" Bearer "+this.$store.state.token}})
        .then(rep=>{
          this.atoms = rep.data.data
          let atoms=this.atoms
          for(let i=0;i<atoms.length;i++)
          {
            this.$set(this.atoms[i],'choice',false)
            this.$set(this.atoms[i],'allow',[])
            if(atoms[i].params!==null)
            {//获取enName然后创建键值
              let enName = atoms[i].params
              enName=enName[0].enName
              this.atoms[i].params=atoms[i].params[0]
              this.$set(this.atoms[i].params,enName,'')
              this.atoms[i].show=true
            }
            else{
              //this.$set(this.atoms[i],'params','')
              //this.atoms[i].params=''
              //his.atoms[i].params.constraints=''
              let params={}
              params.constraints=[]
              this.atoms[i].params=params
              console.log(this.atoms[i].params)
              this.atoms[i].show=false
            }

          }
          console.log(this.atoms)
        })
        .catch(rep=>{
          console.log("ERROR=:"+rep.response)
        })
      console.log(" get() over")

    },
    change(data){
      if(data.choice){
        for (let i=0; i<this.list.length; i++){
          if (this.list[i].id === data.id){
            this.list.splice(i, 1)
          }
        }
        this.list.push(data)
      }
    }
  },
  mounted() {
    this.get()
  }
}
</script>

<style scoped>

</style>
