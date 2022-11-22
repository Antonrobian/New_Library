<script setup>
import { computed, provide, reactive, ref, watchEffect } from "vue";

//
const perpus = reactive({
  data: JSON.parse(localStorage.getItem("Perpustakaan") || "[]"),
  form: {
    id: "",
    tanggal: "",
    namaBuku: null,
    kategori: [],
    author: "",
    status: "",
  },
  selected: {},
  mode: "add",
  filter: {
    search: null,
    status: "",
    kategori: "",
  },
});

watchEffect(() => {
  localStorage.setItem("Perpustakaan", JSON.stringify(perpus.data));
  // console.log(mama.data);
});

const customStatus = (status) => {
  switch (status) {
    case "Dipinjam":
      return " text-bg-success ";
    case "Tersedia":
      return " text-bg-warning ";
    // case "CANCELLED":
    //   return " text-bg-danger ";
    default:
      return " text-bg-dark ";
  }
};

const initForm = () => {
  perpus.form = {
    kategori: [],
    tanggal: null,
    namaBuku: null,
    author: "",
    status: "",
  };
  perpus.mode = "add";
  perpus.selected = {};
};

function createTodo() {
  if (perpus.mode === "add") {
    perpus.data.unshift({
      id: Math.random(),
      tanggal: new Date().toISOString(),
      kategori: perpus.form.kategori,
      tanggal: perpus.form.tanggal,
      namaBuku: perpus.form.namaBuku,
      status: "Tersedia",
      Author: perpus.form.Author,
    });
    perpus.kategori = [];
    perpus.tanggal = null;
    perpus.namaBuku = null;
    perpus.Author = null;
    perpus.form.status = null;

    initForm();
  } else {
    const i = perpus.data.findIndex((item) => item.id === perpus.selected.id);
    perpus.data[i] = {
      ...perpus.selected,
      kategori: perpus.form.kategori,
      tanggal: perpus.form.tanggal,
      namaBuku: perpus.form.namaBuku,
      status: perpus.form.status,
      Author: perpus.form.Author,
    };
    // Reset
    perpus.form.selected = {};
    perpus.form.mode = "add";
    perpus.form.kategori = [];
    perpus.form.tanggal = null;
    perpus.form.namaBuku = null;
    perpus.form.Author = null;
    perpus.form.status = null;
  }
  initForm();
}
//filter
const filterData = computed(() => {
  return perpus.data.filter((item) => {
    if (perpus.filter.kategori) {
      if (item.kategori !== perpus.filter.kategori) {
        return false;
      }
    }
    if (perpus.filter.status) {
      if (item.status !== perpus.filter.status) {
        return false;
      }
    }
    if (perpus.filter.search) {
      if (!item.namaBuku.startsWith(perpus.filter.search)) {
        return false;
      }
    }
    return true;
  });
});

const filteredPerpus = computed(() => {
  return perpus.data;
});

function delPerpus(idx) {
  // const index = perpus.data.findIndex((item) => item.id === id);
  perpus.data.splice(idx, 1);
}
</script>

<template>
  <div class="container">
    <h1>Aplikasi Perpustakaan📖</h1>

    <div class="row">
      <!-- =================== colom inputan =============== -->
      <div class="col-3 border rounded p-2">
        <h5 class="mt-3">Perpustakaan📖</h5>
        <hr />
        <form @submit.prevent="createTodo">
          <h6>Tanggal</h6>
          <input
            class="form-control mb-3"
            v-model="perpus.form.tanggal"
            placeholder="Masukkan Tanggal"
            type="text"
            aria-label="default input example"
          />
          <h6>Nama Buku</h6>
          <input
            class="form-control mb-3"
            placeholder="Masukkan Nama Buku"
            type="text"
            v-model="perpus.form.namaBuku"
            aria-label="default input example"
          />
          <h6>Author</h6>
          <input
            class="form-control mb-3"
            v-model="perpus.form.Author"
            placeholder="Author"
            id="exampleFormControlTextarea1"
            rows="3"
          />

          <div class="row mb-3">
            <h6>Status</h6>
            <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Dipinjam</label
              >
              <input
                class="form-check-input"
                type="radio"
                name="Dipinjam"
                id="flexRadioDefault1"
                value="Dipinjam"
                v-model="perpus.form.status"
              />
            </div>
            <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Tersedia
              </label>
              <input
                class="form-check-input"
                type="radio"
                name="Tersedia"
                value="Tersedia"
                v-model="perpus.form.status"
              />
            </div>
            <div class="row">
              <label class="form-check-label" for="inlineCheckbox1"
                >Kategori</label
              >
              <div class="col">
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox1"
                    value="Novel"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Novel</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox2"
                    value="Majalah"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Majalah</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox1"
                    value="kamus"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Kamus</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox2"
                    value="komik"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Komik</label
                  >
                </div>
              </div>

              <div class="col">
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox1"
                    value="Manga"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Manga</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox2"
                    value="Ensiklopedia"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Ensiklopedia</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.data.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox1"
                    value="Biografi"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Biografi</label
                  >
                </div>
                <div class="form-check form-check-inline">
                  <input
                    v-model="perpus.form.kategori"
                    class="form-check-input"
                    type="checkbox"
                    id="inlineCheckbox2"
                    value="Naskah"
                  />
                  <label class="form-check-label" for="inlineCheckbox1"
                    >Naskah</label
                  >
                </div>
              </div>
            </div>
            <!-- <div class="col">
              <label class="form-check-label me-1" for="flexRadioDefault1"
                >Canceled
              </label>
              <input
                class="form-check-input"
                type="radio"
                name="flexRadioDefault"
                id="flexRadioDefault1"
                value="CANCELLED"
                v-model="mama.form.status"
              />
            </div> -->
          </div>

          <button type="submit" class="btn btn-primary">
            {{ perpus.mode === "add" ? "Simpan" : "Edit" }}
          </button>
          <button v-if="perpus.mode === 'edit'" class="btn btn-primary">
            batal
          </button>
        </form>
      </div>

      <!-- ============== colom data =================== -->
      <div class="col-8 ms-4">
        <div class="row">
          <div class="col">
            <input
              type="text"
              class="form-control"
              placeholder="Cari"
              aria-label="First name"
              v-model="perpus.filter.search"
            />
          </div>
          <div class="col">
            <select
              class="form-select mb-3"
              aria-label="Default select example"
              v-model="perpus.filter.kategori"
            >
              <option :value="''" disabled>Filter By Kategori</option>
              <option value="Novel">Novel</option>
              <option value="Majalah">Majalah</option>
              <option value="Kamus">Kamus</option>
              <option value="Komik">Komik</option>
              <option value="Manga">Manga</option>
              <option value="Ensiklopedia">Ensiklopedia</option>
              <option value="Biografi">Biografi</option>
              <option value="Naska">Naska</option>
            </select>
          </div>
          <div class="col">
            <select
              class="form-select mb-3"
              aria-label="Default select example"
              v-model="perpus.filter.status"
            >
              <option :value="''">Filter by Status</option>
              <option value="Dipinjam">Dipinjam</option>
              <option value="Tersedia">Tersedia</option>
              <!-- <option value="CANCELLED">CANCELED</option> -->
            </select>
          </div>
          <div
            class="col"
            v-if="
              perpus.filter.search ||
              perpus.filter.kategori ||
              perpus.filter.status
            "
          >
            <button
              class="btn btn-dark"
              @click="
                perpus.filter.search = '';
                perpus.filter.kategori = '';
                perpus.filter.status = '';
              "
            >
              reset
            </button>
          </div>
        </div>
        <table class="table border-top">
          <thead>
            <tr>
              <th scope="col">ID</th>
              <th scope="col">DATE</th>
              <th scope="col">KATEGORI</th>
              <th scope="col">NAMABUKU</th>
              <th scope="col">AUTHOR</th>
              <th scope="col">STATUS</th>
              <th scope="col">ACTION</th>
            </tr>
          </thead>
          <tbody v-if="filterData.length">
            <tr v-for="(item, index) in filterData" :key="item.id">
              <td>{{ item.id }}</td>
              <td>{{ item.tanggal }}</td>
              <td>{{ item.kategori }}</td>
              <td>{{ item.namaBuku }}</td>
              <td>{{ item.Author }}</td>
              <td>
                <span :class="customStatus(item.status) + ' badge '">
                  {{ item.status }}
                </span>
              </td>
              <td>
                <button
                  type="button"
                  @click="
                    perpus.mode = 'edit';
                    perpus.selected = item;
                    perpus.form = { ...item };
                  "
                  class="btn btn-success mx-1"
                >
                  ✍
                </button>
                <button
                  type="button"
                  @click="delPerpus(index)"
                  class="btn btn-danger"
                >
                  ❌
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style>
* {
  font-size: 0.9rem;
}

.container {
  margin-top: 1rem;
}
</style>
