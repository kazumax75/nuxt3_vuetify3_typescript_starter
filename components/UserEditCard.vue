<script lang="ts" setup>
import { User } from "@/types/User";

const props = defineProps<{ modelValue: User }>();
const emit = defineEmits<{ (e: "update:modelValue", value: User): void }>();

// プルダウンのリスト定義
const cityItem = [
  "Gwenborough",
  "Wisokyburgh",
  "McKenziehaven",
  "South Elvis",
  "Roscoeview",
  "South Christy",
  "Howemouth",
  "Aliyaview",
  "Bartholomebury",
  "Lebsackbury",
];

const firstName = computed({
  get: (): string => {
    return props.modelValue.name.split(" ")[0] ?? "";
  },
  set: (_n: string) => {
    let name = _n + " " + lastName.value;
    emit("update:modelValue", { ...props.modelValue, ...{ name: name } });
  },
});

const lastName = computed({
  get: (): string => {
    return props.modelValue.name.split(" ")[1] ?? "";
  },
  set: (_n: string) => {
    let name = firstName.value + " " + _n;
    emit("update:modelValue", { ...props.modelValue, ...{ name: name } });
  },
});
</script>

<template>
  <v-card class="pa-5 ma-6" elevation="2">
    <v-card-title>{{ modelValue.name }}</v-card-title>

    <v-row>
      <v-col cols="12" sm="2" md="2">
        <v-text-field
          v-model.number="modelValue.id"
          type="number"
          step="1"
          label="ID"
          bg-color="white"
        ></v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" sm="6" md="3">
        <v-text-field
          v-model="firstName"
          label="firstName"
          bg-color="white"
        ></v-text-field>
      </v-col>

      <v-col cols="12" sm="6" md="3">
        <v-text-field
          v-model="lastName"
          label="lastName"
          bg-color="white"
        ></v-text-field>
      </v-col>
    </v-row>

    <v-text-field
      v-model="modelValue.email"
      label="email"
      placeholder="Placeholder"
      variant="outlined"
      bg-color="white"
    ></v-text-field>
    <v-select
      v-model="modelValue.address.city"
      :items="cityItem"
      label="city"
      solo
    ></v-select>
  </v-card>
</template>

<style scoped></style>
