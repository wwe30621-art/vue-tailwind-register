<script setup>
import { reactive, computed } from "vue";

const ui = {
  input:
    "w-full px-4 py-3 text-sm placeholder:text-slate-400 " +
    "bg-slate-50 border border-slate-200 rounded-xl " +
    "focus:bg-white focus:outline-none focus:ring-4 focus:ring-slate-900/5",

  label: "block text-sm font-medium text-slate-700",

  card: "w-full max-w-md bg-white rounded-2xl shadow-sm border border-slate-200",

  sectionTitle: "text-sm font-semibold text-slate-900",

  btnPrimary:
    "w-full px-4 py-3 text-sm font-semibold text-white " +
    "bg-slate-900 rounded-xl hover:bg-slate-800 active:bg-slate-950 disabled:opacity-50 disabled:cursor-not-allowed",
};

const form = reactive({
  role: "owner", // 'owner' | 'shop'

  
  email: "",
  phone: "",
  password: "",
  passwordConfirm: "",
  agree: false,

  owner: { city: "", plate: "" },
  shop: { name: "", taxId: "", city: "", address: "" },
});

const isOwner = computed(() => form.role === "owner");
</script>

<template>
  <div class="min-h-screen bg-slate-50 flex items-center justify-center px-4 py-10">
    <div :class="ui.card">
      <div class="p-8 border-b border-slate-100">
        <p class="text-xs text-slate-500">Car E</p>
        <h1 class="mt-2 text-2xl font-bold text-slate-900 leading-tight">
          註冊帳號
        </h1>
        <p class="mt-3 text-sm text-slate-600">
          選擇身分後填寫基本資料即可完成註冊。
        </p>
      </div>

      <form class="p-8 space-y-6" @submit.prevent>
        <!-- 身分選擇 -->
        <section class="space-y-3">
          <h2 class="text-sm font-semibold text-slate-900">註冊身分</h2>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-3">
            <label
              class="rounded-2xl border p-4 cursor-pointer flex gap-3"
              :class="form.role === 'owner' ? 'border-slate-900 bg-slate-50' : 'border-slate-200 bg-white'"
            >
              <input v-model="form.role" type="radio" value="owner" class="mt-1 h-4 w-4" />
              <div>
                <p class="text-sm font-semibold text-slate-900">車主</p>
                <p class="mt-1 text-xs text-slate-600">我要找保養廠維修保養</p>
              </div>
            </label>

            <label
              class="rounded-2xl border p-4 cursor-pointer flex gap-3"
              :class="form.role === 'shop' ? 'border-slate-900 bg-slate-50' : 'border-slate-200 bg-white'"
            >
              <input v-model="form.role" type="radio" value="shop" class="mt-1 h-4 w-4" />
              <div>
                <p class="text-sm font-semibold text-slate-900">保養廠</p>
                <p class="mt-1 text-xs text-slate-600">我要上架服務並接單</p>
              </div>
            </label>
          </div>
        </section>

        <!-- 帳號資料 -->
        <section class="space-y-4">
          <h2 class="text-sm font-semibold text-slate-900">帳號資料</h2>

          <div>
            <label :class="ui.label">Email</label>
            <input v-model="form.email" type="email" autocomplete="email" placeholder="name@example.com" :class="ui.input" />
          </div>

          <div>
            <label :class="ui.label">手機號碼</label>
            <input v-model="form.phone" type="tel" autocomplete="tel" placeholder="例如：09xxxxxxxx" :class="ui.input" />
          </div>

          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <div>
              <label :class="ui.label">密碼</label>
              <input v-model="form.password" type="password" autocomplete="new-password" placeholder="至少 8 碼" :class="ui.input" />
            </div>
            <div>
              <label :class="ui.label">確認密碼</label>
              <input v-model="form.passwordConfirm" type="password" autocomplete="new-password" placeholder="再輸入一次" :class="ui.input" />
            </div>
          </div>
        </section>

        <!-- 身分資料 -->
        <section class="space-y-4">
          <h2 class="text-sm font-semibold text-slate-900">
            {{ isOwner ? "車主資料（可先簡填）" : "保養廠資料" }}
          </h2>

          <template v-if="isOwner">
            <div>
              <label :class="ui.label">縣市</label>
              <input v-model="form.owner.city" type="text" placeholder="例如：台北市" :class="ui.input" />
            </div>
            <div>
              <label :class="ui.label">車牌（選填）</label>
              <input v-model="form.owner.plate" type="text" placeholder="例如：ABC-1234" :class="ui.input" />
            </div>
          </template>

          <template v-else>
            <div>
              <label :class="ui.label">保養廠名稱</label>
              <input v-model="form.shop.name" type="text" placeholder="例如：XX 汽車保修中心" :class="ui.input" />
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
              <div>
                <label :class="ui.label">統一編號</label>
                <input v-model="form.shop.taxId" type="text" placeholder="8 碼統編" :class="ui.input" />
              </div>
              <div>
                <label :class="ui.label">縣市</label>
                <input v-model="form.shop.city" type="text" placeholder="例如：台中市" :class="ui.input" />
              </div>
            </div>

            <div>
              <label :class="ui.label">地址</label>
              <input v-model="form.shop.address" type="text" placeholder="路名/巷弄/號/樓" :class="ui.input" />
            </div>
          </template>
        </section>

        <!-- 同意條款 -->
        <label class="flex items-start gap-3 text-sm text-slate-700">
          <input v-model="form.agree" type="checkbox" class="mt-1 h-4 w-4" />
          <span>
            我已閱讀並同意
            <span class="text-slate-900 font-medium">服務條款</span> 與
            <span class="text-slate-900 font-medium">隱私權政策</span>
          </span>
        </label>

        <button type="submit" :class="ui.btnPrimary" :disabled="!form.agree">
          建立帳號
        </button>
      </form>
    </div>
  </div>
</template>
