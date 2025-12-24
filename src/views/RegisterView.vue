<script setup lang="ts">
import { computed, reactive, ref } from "vue";

/* ---------- step control ---------- */
const step = ref(1);

/* ---------- form state ---------- */
const form = reactive({
	role: "owner", // owner | shop

	/* common */
	email: "",
	phone: "",
	password: "",
	confirmPassword: "",

	/* owner */
	name: "",
	nickname: "",

	/* shop */
	shopName: "",
	taxId: "",
	city: "",
	district: "",
	address: "",

	agree: false,
});

/* ---------- touched / submitted flags ---------- */
const touched = reactive({
	email: false,
	phone: false,
	password: false,
	confirmPassword: false,

	name: false,
	nickname: false,

	shopName: false,
	taxId: false,
	city: false,
	district: false,
	address: false,

	agree: false,
});

/* ---------- error state ---------- */
const errors = reactive({
	email: "",
	phone: "",
	password: "",
	confirmPassword: "",

	name: "",
	nickname: "",

	shopName: "",
	taxId: "",
	city: "",
	district: "",
	address: "",

	agree: "",
});

/* ---------- ui classes ---------- */
const ui = {
	page: "min-h-screen bg-slate-50 px-4 py-10",
	wrap: "mx-auto w-full max-w-md",
	card: "rounded-2xl border border-slate-200 bg-white p-6 shadow-sm",
	h1: "text-xl font-semibold text-slate-900",
	sectionTitle: "text-sm font-semibold text-slate-900",
	label: "block text-sm font-medium text-slate-700",

	inputBase:
		"w-full rounded-xl border px-4 py-3 text-sm placeholder:text-slate-400 focus:outline-none focus:ring-4",
	inputOk: "border-slate-200 bg-slate-50 focus:bg-white focus:ring-slate-900/5",
	inputErr: "border-red-500 bg-red-50 focus:ring-red-500/20",

	errorText: "mt-1 text-xs text-red-600",

	btnPrimary:
		"w-full rounded-xl bg-slate-900 px-4 py-3 text-sm font-semibold text-white hover:bg-slate-800 active:bg-slate-950 disabled:opacity-50",
	btnGhost:
		"w-full rounded-xl border border-slate-200 bg-white px-4 py-3 text-sm font-semibold text-slate-800",

	grid2: "grid grid-cols-1 gap-4 sm:grid-cols-2",
	divider: "my-6 border-t border-slate-100",
};

/* ---------- computed ---------- */
const isShop = computed(() => form.role === "shop");

/* ---------- helpers ---------- */
function isEmail(v) {
	return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(v);
}
function isPhone(v) {
	return /^(09\d{8}|\+8869\d{8})$/.test(v);
}
function showError(key) {
	return touched[key] && !!errors[key];
}
function inputClass(key) {
	return [ui.inputBase, showError(key) ? ui.inputErr : ui.inputOk].join(" ");
}
function markTouched(keys) {
	keys.forEach((k) => (touched[k] = true));
}
function clearErrors(keys) {
	keys.forEach((k) => (errors[k] = ""));
}

/* ---------- validation (per field) ---------- */
function validateEmail() {
	errors.email = "";
	if (!form.email) errors.email = "請輸入電子郵件";
	else if (!isEmail(form.email)) errors.email = "Email 格式錯誤";
}
function validatePhone() {
	errors.phone = "";
	if (!form.phone) errors.phone = "請輸入手機號碼";
	else if (!isPhone(form.phone)) errors.phone = "手機格式錯誤（例：09xxxxxxxx / +8869xxxxxxxx）";
}
function validatePassword() {
	errors.password = "";
	if (!form.password) errors.password = "請輸入密碼";
	else if (form.password.length < 8) errors.password = "密碼至少 8 碼";
}
function validateConfirmPassword() {
	errors.confirmPassword = "";
	if (!form.confirmPassword) errors.confirmPassword = "請再次輸入密碼";
	else if (form.confirmPassword !== form.password) errors.confirmPassword = "密碼不一致";
}
function validateOwnerFields() {
	errors.name = "";
	errors.nickname = "";
	if (!form.name) errors.name = "請輸入姓名";
	if (!form.nickname) errors.nickname = "請輸入暱稱";
}
function validateShopFields() {
	errors.shopName = "";
	errors.taxId = "";
	errors.city = "";
	errors.district = "";
	errors.address = "";

	if (!form.shopName) errors.shopName = "請輸入保養廠名稱";

	if (!form.taxId) errors.taxId = "請輸入統一編號";
	else if (!/^\d{8}$/.test(form.taxId)) errors.taxId = "統編需 8 碼數字";

	if (!form.city) errors.city = "請輸入縣市";
	if (!form.district) errors.district = "請輸入區域";
	if (!form.address) errors.address = "請輸入地址";
}
function validateAgree() {
	errors.agree = "";
	if (!form.agree) errors.agree = "請勾選同意服務條款與隱私權政策";
}

/* ---------- step validators ---------- */
function validateStep1() {
	const keys = ["email"];
	markTouched(keys);
	clearErrors(keys);
	validateEmail();
	return !errors.email;
}

function validateStep2() {
	const commonKeys = ["phone", "password", "confirmPassword"];
	const ownerKeys = ["name", "nickname"];
	const shopKeys = ["shopName", "taxId", "city", "district", "address"];

	if (isShop.value) markTouched([...commonKeys, ...shopKeys]);
	else markTouched([...commonKeys, ...ownerKeys]);

	clearErrors([...commonKeys, ...ownerKeys, ...shopKeys]);

	validatePhone();
	validatePassword();
	validateConfirmPassword();

	if (isShop.value) validateShopFields();
	else validateOwnerFields();

	const keysToCheck = isShop.value
		? [...commonKeys, ...shopKeys]
		: [...commonKeys, ...ownerKeys];

	return !keysToCheck.some((k) => !!errors[k]);
}

function validateStep3() {
	const keys = ["agree"];
	markTouched(keys);
	clearErrors(keys);
	validateAgree();
	return !errors.agree;
}

/* ---------- actions ---------- */
function next() {
	if (step.value === 1 && validateStep1()) step.value = 2;
	else if (step.value === 2 && validateStep2()) step.value = 3;
}
function back() {
	if (step.value > 1) step.value--;
}
function submit() {
	if (!validateStep3()) return;
	alert("註冊完成（示範）");
}

/* ---------- input blur handlers ---------- */
function onBlur(key) {
	touched[key] = true;

	if (key === "email") validateEmail();
	if (key === "phone") validatePhone();
	if (key === "password") validatePassword();
	if (key === "confirmPassword") validateConfirmPassword();

	if (key === "name" || key === "nickname") validateOwnerFields();

	if (
		key === "shopName" ||
		key === "taxId" ||
		key === "city" ||
		key === "district" ||
		key === "address"
	) {
		validateShopFields();
	}
}
</script>

<template>
	<div :class="ui.page">
		<div :class="ui.wrap">
			<h1 :class="ui.h1" class="mb-2">註冊</h1>
			<p class="mb-4 text-xs text-slate-500">步驟 {{ step }} / 3</p>

			<div :class="ui.card">
				<!-- STEP 1 -->
				<section v-if="step === 1" class="space-y-4">
					<h2 :class="ui.sectionTitle">註冊第一步</h2>

					<div>
						<label :class="ui.label">Email</label>
						<input
							v-model="form.email"
							:class="inputClass('email')"
							@blur="onBlur('email')"
							placeholder="name@example.com"
						/>
						<p v-if="showError('email')" :class="ui.errorText">{{ errors.email }}</p>
					</div>

					<button :class="ui.btnPrimary" @click="next">下一步</button>
				</section>

				<!-- STEP 2 -->
				<section v-if="step === 2" class="space-y-6">
					<h2 :class="ui.sectionTitle">帳號資料</h2>

					<!-- role -->
					<div class="flex gap-3">
						<label class="flex items-center gap-2">
							<input type="radio" value="owner" v-model="form.role" />
							車主（一般消費）
						</label>
						<label class="flex items-center gap-2">
							<input type="radio" value="shop" v-model="form.role" />
							保養廠（店家）
						</label>
					</div>

					<!-- owner required -->
					<div v-if="!isShop" class="space-y-4">
						<div>
							<label :class="ui.label">姓名</label>
							<input v-model="form.name" :class="inputClass('name')" @blur="onBlur('name')" />
							<p v-if="showError('name')" :class="ui.errorText">{{ errors.name }}</p>
						</div>

						<div>
							<label :class="ui.label">暱稱</label>
							<input
								v-model="form.nickname"
								:class="inputClass('nickname')"
								@blur="onBlur('nickname')"
							/>
							<p v-if="showError('nickname')" :class="ui.errorText">{{ errors.nickname }}</p>
						</div>
					</div>

					<!-- common -->
					<div>
						<label :class="ui.label">手機號碼</label>
						<input
							v-model="form.phone"
							:class="inputClass('phone')"
							@blur="onBlur('phone')"
							placeholder="例如：09xxxxxxxx 或 +8869xxxxxxxx"
						/>
						<p v-if="showError('phone')" :class="ui.errorText">{{ errors.phone }}</p>
					</div>

					<div :class="ui.grid2">
						<div>
							<label :class="ui.label">密碼</label>
							<input
								type="password"
								v-model="form.password"
								:class="inputClass('password')"
								@blur="onBlur('password')"
								placeholder="至少 8 碼"
							/>
							<p v-if="showError('password')" :class="ui.errorText">{{ errors.password }}</p>
						</div>

						<div>
							<label :class="ui.label">確認密碼</label>
							<input
								type="password"
								v-model="form.confirmPassword"
								:class="inputClass('confirmPassword')"
								@blur="onBlur('confirmPassword')"
								placeholder="再輸入一次"
							/>
							<p v-if="showError('confirmPassword')" :class="ui.errorText">
								{{ errors.confirmPassword }}
							</p>
						</div>
					</div>

					<!-- shop required -->
					<div v-if="isShop" class="space-y-4">
						<hr :class="ui.divider" />

						<div>
							<label :class="ui.label">保養廠名稱</label>
							<input
								v-model="form.shopName"
								:class="inputClass('shopName')"
								@blur="onBlur('shopName')"
								placeholder="例如：XX 汽車保修中心"
							/>
							<p v-if="showError('shopName')" :class="ui.errorText">{{ errors.shopName }}</p>
						</div>

						<div>
							<label :class="ui.label">統一編號</label>
							<input
								v-model="form.taxId"
								:class="inputClass('taxId')"
								@blur="onBlur('taxId')"
								placeholder="8 碼數字"
							/>
							<p v-if="showError('taxId')" :class="ui.errorText">{{ errors.taxId }}</p>
						</div>

						<div :class="ui.grid2">
							<div>
								<label :class="ui.label">縣市</label>
								<input v-model="form.city" :class="inputClass('city')" @blur="onBlur('city')" />
								<p v-if="showError('city')" :class="ui.errorText">{{ errors.city }}</p>
							</div>

							<div>
								<label :class="ui.label">區域</label>
								<input
									v-model="form.district"
									:class="inputClass('district')"
									@blur="onBlur('district')"
								/>
								<p v-if="showError('district')" :class="ui.errorText">{{ errors.district }}</p>
							</div>
						</div>

						<div>
							<label :class="ui.label">地址</label>
							<input
								v-model="form.address"
								:class="inputClass('address')"
								@blur="onBlur('address')"
								placeholder="路名/巷弄/號/樓"
							/>
							<p v-if="showError('address')" :class="ui.errorText">{{ errors.address }}</p>
						</div>
					</div>

					<div class="grid grid-cols-2 gap-3">
						<button :class="ui.btnGhost" @click="back">上一步</button>
						<button :class="ui.btnPrimary" @click="next">下一步</button>
					</div>
				</section>

				<!-- STEP 3 -->
				<section v-if="step === 3" class="space-y-4">
					<h2 :class="ui.sectionTitle">確認送出</h2>

					<label class="flex items-center gap-2 text-sm text-slate-700">
						<input
							type="checkbox"
							v-model="form.agree"
							@blur="touched.agree = true"
						/>
						我已閱讀並同意服務條款與隱私權政策
					</label>
					<p v-if="touched.agree && errors.agree" :class="ui.errorText">{{ errors.agree }}</p>

					<div class="grid grid-cols-2 gap-3">
						<button :class="ui.btnGhost" @click="back">上一步</button>
						<button :class="ui.btnPrimary" @click="submit">建立帳號</button>
					</div>
				</section>
			</div>
		</div>
	</div>
</template>

