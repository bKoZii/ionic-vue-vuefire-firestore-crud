<template>
  <div v-if="data">
    <ion-item-sliding>
      <ion-item @click="openModal(memberDetailModal, data)" button :detail=true lines="full">
        <ion-label>{{
          data.title + ' ' + data.fName + ' ' + data.lName
          }}</ion-label>
        <ion-icon v-if="data.isVerified == true" :icon="checkmarkCircle" slot="start" color="primary"></ion-icon>
        <ion-icon v-else :icon="alertCircle" slot="start" color="warning"></ion-icon>
      </ion-item>
      <ion-item-options>
        <ion-item-option @click="openModal(editMemberModal, data)"><ion-icon
            :icon="create"></ion-icon></ion-item-option>
        <ion-item-option color="danger" @click="delMember(data)"><ion-icon
            :icon="trashBin"></ion-icon></ion-item-option>
      </ion-item-options>
    </ion-item-sliding>
  </div>
</template>
<script setup lang="ts">
import {
  IonItem,
  IonItemSliding,
  IonLabel,
  IonItemOptions,
  IonItemOption,
  alertController,
  IonIcon,
} from '@ionic/vue'
import { create, trashBin, checkmarkCircle, alertCircle } from 'ionicons/icons'
import { deleteMember } from '@/services/FirebaseService'
import { memberToast, openModal } from '@/services/UtilService'
import { MemberInterface } from '@/interface/MemberInterface'
import { defineAsyncComponent } from 'vue'
const memberDetailModal = defineAsyncComponent(
  () => import('@/components/MemberDetail.vue')
)
const editMemberModal = defineAsyncComponent(
  () => import('@/components/EditMember.vue')
)

defineProps<{
  data: MemberInterface
}>()

const delMember = async (data: any) => {
  const alert = await alertController.create({
    header: 'Deleting Member',
    subHeader: 'Are you sure?',
    message: data.title + ' ' + data.fName + ' ' + data.lName,
    buttons: [
      {
        text: 'Cancel',
        role: 'cancel',
      },
      {
        text: 'Delete',
        role: 'destructive',
        handler: () => {
          deleteMember(data.id)
            .then(async (message) => {
              await memberToast(message)
            })
            .catch(async (error) => {
              await memberToast(error.message, 'danger')
            })
        },
      },
    ],
  })
  alert.present()
}
</script>
<style scoped>
ion-item-option ion-icon {
  width: 1.2rem;
  height: 1.2rem;
}
</style>
