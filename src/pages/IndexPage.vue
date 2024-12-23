<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" :rules="[validateNotEmpty]"
        lazy-rules/>
        <q-input v-model="tempData.age" label="年齡" type="number" :rules="[validateNotEmpty, validatePositiveInteger]" lazy-rules/>
        <q-btn color="primary" class="q-mt-md" @click="addRow">新增</q-btn>
      </div>

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
interface btnType {
  label: string;
  icon: string;
  status: string;
}
const blockData = ref([
  {
    name: 'test',
    age: 25,
  },
]);
const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);
const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});

const validateNotEmpty = (value: string) => {
  return value?.trim() ? true : '此欄位不得為空白';
};

const validatePositiveInteger = (value: number) => {
  return Number.isInteger(value) && value > 0 ? true : '請輸入正整數';
};

async function fetchData() {
  try {
    const response = await axios.get('https://dahua.metcfire.com.tw/api/CRUDTest/a');
    blockData.value = response.data || [];
  } catch (error) {
    console.error('查詢資料失敗：', error);
  }
}

async function addRow() {
  try {
    const isNameValid = validateNotEmpty(tempData.value.name) === true;
    const isAgeValid = validateNotEmpty(tempData.value.age) === true && 
                       validatePositiveInteger(Number(tempData.value.age)) === true;

    if (!isNameValid || !isAgeValid) {
      console.error('驗證失敗：請確認表單資料');
      return;
    }
    const response = await axios.post('https://dahua.metcfire.com.tw/api/CRUDTest', {
      name: tempData.value.name,
      age: Number(tempData.value.age),
    });
    blockData.value.push(response.data);
    tempData.value = { id: '', name: '', age: null };
  } catch (error) {
    console.error('新增資料失敗：', error);
  }
}
async function editRow(data) {
  try {
    await axios.patch(`https://dahua.metcfire.com.tw/api/CRUDTest`, data);
    
  } catch (error) {
    console.error('修改資料失敗：', error);
  }
}

async function deleteRow(id) {
  try {
    await axios.delete(`https://dahua.metcfire.com.tw/api/CRUDTest/${id}`);
  } catch (error) {
    console.error('刪除資料失敗：', error);
  }
}

function handleClickOption(btn, data) {
  if (btn.status === 'edit') {
    tempData.value = { ...data };
  } else if (btn.status === 'delete') {
    deleteRow(data.id); 
  }
}
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
