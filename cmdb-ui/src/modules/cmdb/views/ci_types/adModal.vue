<template>
  <a-modal width="800px" :visible="visible" @ok="handleOK" @cancel="handleCancel" :closable="false">
    <Discovery
      :isSelected="true"
      :style="{ maxHeight: '75vh', overflow: 'auto' }"
      v-if="visible"
    />
    <template #footer>
      <a-space>
        <a-button @click="handleCancel">{{ $t('cancel') }}</a-button>
        <a-button type="primary" @click="handleOK">{{ $t('confirm') }}</a-button>
        <a-button type="primary" @click="addPlugin">{{ $t('cmdb.ciType.addPlugin') }}</a-button>
      </a-space>
    </template>
  </a-modal>
</template>

<script>
import _ from 'lodash'
import Discovery from '../discovery'

export default {
  name: 'ADModal',
  components: { Discovery },
  props: {
    CITypeId: {
      type: Number,
      default: null,
    },
  },
  data() {
    return {
      visible: false,
      selectedIds: [],
    }
  },
  provide() {
    return {
      setSelectedIds: this.setSelectedIds,
      selectedIds: () => {
        return this.selectedIds
      },
    }
  },
  inject: ['getCITypeDiscovery'],
  methods: {
    open() {
      this.visible = true
    },
    handleCancel() {
      this.selectedIds = []
      this.visible = false
    },
    async handleOK() {
      if (this.selectedIds && this.selectedIds.length) {
        const adCITypeList = this.selectedIds.map((item, index) => {
          return {
            adr_id: item.id,
            id: new Date().getTime() + index,
            extra_option: {
              alias: ''
            },
            isClient: true,
          }
        })
        this.$emit('pushCITypeList', adCITypeList)
      }
      this.handleCancel()
    },
    setSelectedIds(id, type) {
      const _selectedIds = _.cloneDeep(this.selectedIds)
      const _idx = _selectedIds.findIndex((item) => item.id === id)
      if (_idx > -1) {
        _selectedIds.splice(_idx, 1)
      } else {
        _selectedIds.push({ id, type })
      }
      this.selectedIds = _selectedIds
    },
    addPlugin() {
      this.handleCancel()
      this.$emit('addPlugin')
    },
  },
}
</script>

<style></style>
