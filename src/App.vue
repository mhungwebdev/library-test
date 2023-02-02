<template>
  <div>
    <div>
      <dx-button @click="show">
        Show value
      </dx-button>
    </div>
    <div>
      <dx-data-grid ref="dataConfig">
        <template #groupTemplate="{data}">
          <div>{{ data.value }}</div>
        </template>

        <template #pointTemplate="{data:cellInfo}">
          <input type="number" :value="cellInfo.value" @input="e => cellInfo.setValue(e.target.value)" />
        </template>

        <template #noteTemplate="{data:cellInfo}">
          <input v-model="cellInfo.data.Note" />
        </template>

        <template #headerTemplate="{data}">
          <div v-html="data.column.caption"></div>
        </template>
      </dx-data-grid>
    </div>
  </div>
</template>

<script setup lang="ts">
import { DxDataGrid } from "devextreme-vue/data-grid";
import DxButton from 'devextreme-vue/button';
import { ref, onMounted, reactive } from "vue";
import { CapacityCriteria, PointUser } from "./data";

const dataConfig = ref<DxDataGrid>();
const CapacityCriteriaClone = reactive(CapacityCriteria);

const logData = (data:any) => {
  console.log(data);
}

const config = reactive<DxDataGrid>({
  dataSource: CapacityCriteriaClone,
  columns: [
    {
      dataField: "CriteriaName",
      caption: "Tên tiêu chí",
      allowEditing:false
    },
    {
      dataField: "Proportion",
      caption: "Tỷ trọng",
      allowEditing:false
    },
    {
      dataField:'Point',
      caption:'Điểm',
      dataType:'number',
      editCellTemplate:'pointTemplate',
      allowEditing:true,
      showEditorAlways:true,
      editorOptions:{
        mode:'cell'
      }
    },{
      caption:"Tự đánh giá",
      // dataField:'CriteriaID',
      columns:[{
        dataField:'CriteriaID',
        caption:'<span style="color:red">Họ và tên : </span>Lê Mạnh Hùng',
        headerCellTemplate:'headerTemplate',
        lookup:{
          dataSource:PointUser,
          displayExpr:'PointReview',
          valueExpr:'CriteriaID'
        },
      }]
    },
    {
      dataField: "CriteriaGroupName",
      groupIndex: 0,
      width: 120,
      groupCellTemplate:'groupTemplate'
    },{
      dataField: "Note",
      width: 120,
      editCellTemplate:'noteTemplate',
      showEditorAlways:true
    }
  ],
  grouping: {
    allowCollapsing: false,
  },
  showBorders:true,
  twoWayBindingEnabled:false,
  height:'100%',
  keyExpr:'ID',
  loadPanel:{
    enabled:false
  },
  editing:{
    allowUpdating:true,
    mode:'cell',
  },
  summary:{
    totalItems:[
      {
        column:'Point',
        summaryType:'sum',
      },
      {
        column:'CriteriaName',
        customizeText:() => 'Tổng',
      }
    ]
  }
});

onMounted(() => {
  dataConfig.value?.instance?.option(config);
});

const show = () => {
  console.log(dataConfig.value?.instance?.option('dataSource'));
}

const changeValue = (key:any,e:any) => {
  console.log({key,e});
}
</script>

<style scoped>
.dx-editor-cell.dx-focused {
  border: none;
}
</style>
