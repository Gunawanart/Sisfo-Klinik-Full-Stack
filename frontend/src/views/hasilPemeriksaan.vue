<template>
  <div class="app">
    <nav>
      <div class="flexlogo">
        <div class="logo">
          <router-link to="/">
            <img src="../../public/img/kliniku.svg" alt />
          </router-link>
        </div>
        <div class="back" v-if="isFull">
          <img src="img/arrow.png" alt />
          <router-link to="/dashboard-pasien">
            <p>kembali ke halaman utama</p>
          </router-link>
        </div>
        <div class="back" v-else @click="switchPage(false)" style="cursor:pointer;">
          <img src="../../public/img/arrow.png" alt />
          <a>
            <p>kembali</p>
          </a>
        </div>
      </div>
      <div class="user">
        <div class="help"></div>
        <div class="nama">
          <!--username dan status sesuai dengan username yang sudah login/register-->
          <h1 class="username" @click="showMenu">{{ username }}</h1>
          <p class="status">patient account</p>
          <div class="activity">
            <div class="kelola-akun" @click="$router.push({path :'/account'})">
              <p>kelola akun</p>
              <img src="../../public/img/arrow.png" alt />
            </div>
            <div class="logout" @click="logout">
              <p>logout</p>
              <img src="../../public/img/arrow.png" alt />
            </div>
          </div>
        </div>
        <div class="foto">
          <div class="foto-user" v-bind:style="{ backgroundImage: 'url(' + avatar + ')' }"></div>
          <div class="data">
            <router-link to="/data-pasien">kelola data</router-link>
            <img src="../../public/img/arrow.png" alt />
          </div>
        </div>
      </div>
    </nav>
    <div class="container">
      <div class="teks">
        <section v-if="isFull">
          <h1>hasil</h1>
          <h1>pemeriksaan</h1>
        </section>
        <h1 class="small-ver" v-else>hasil pemeriksaan</h1>
        <p>cek hasil pemeriksaan kamu dari dokter</p>
      </div>
      <div class="dokter">
        <img src="../../public/img/hasil.png" alt />
      </div>
      <div class="fungsi" v-if="isFull">
        <section v-for="(konsul,index) in konsul" :key="konsul._id" @click="switchPage(index)">
          <div class="riwayat">
            <img src="../../public/img/frame.png" alt />
            <h1>{{ date(konsul.tanggal) }}</h1>
            <p>
              konsultasi kamu
              sudah ditanggapi
            </p>
          </div>
        </section>
      </div>
      <div class="detail" v-else>
        <h2 class="tanggal">{{ tanggal }}</h2>
        <div class="diagnosis">
          <h2>diagnosis penyakit</h2>
          <p>{{ diagnosis }}</p>
        </div>
        <div class="obat">
          <h2>obat</h2>
          <div class="list-obat" v-for="obat in obat" :key="obat._id">
            <div class="nama">
              <p>{{ obat.obat }}</p>
              <p>{{ obat.quantity }}</p>
            </div>
            <div class="aturan">
              <p>aturan pakai</p>
              <p>{{ obat.catatan }}</p>
            </div>
          </div>
        </div>

        <div class="catatan">
          <h2>catatan dokter</h2>
          <p>{{ catatan }}</p>
        </div>
      </div>
    </div>

    <div class="wave">
      <img src="../../public/img/wave2.png" alt />
    </div>
    <div class="dot">
      <img src="../../public/img/dot.png" alt />
    </div>
  </div>
</template>

<script>
import { logout } from "../../mixin";
import axios from "axios";
export default {
  mixins: [logout],
  data() {
    return {
      username: "",
      avatar: "",
      konsul: [],
      isFull: true,
      tanggal: "",
      diagnosis: "",
      obat: [],
      catatan: "",
    };
  },
  created() {
    let token = localStorage.getItem("token");

    axios
      .get("/getUser", {
        headers: {
          Authorization: "Bearer " + token,
        },
      })
      .then((res) => {
        this.username = res.data.user.username;
        this.avatar = axios.defaults.baseURL + "/" + res.data.user.avatar;
      });

    axios
      .get("/get-hasil", {
        headers: {
          Authorization: "Bearer " + token,
        },
      })
      .then((res) => {
        console.log(res);

        this.konsul = res.data.konsul;
      })
      .catch((err) => {
        console.log(err);
      });
  },
  methods: {
    showMenu() {
      const help = document.querySelector(".help");
      const activity = document.querySelector(".activity");
      help.classList.toggle("show");
      activity.classList.toggle("show");
    },
    date(detail) {
      console.log(detail);
      let dt = this.monthChange(detail.slice(4, 8).replace(/\s/g, ""));
      return detail.slice(8, 11) + dt + detail.slice(10, 16);
    },
    switchPage(index) {
      if (index === false) {
        this.isFull = !this.isFull;

        return;
      }

      this.isFull = !this.isFull;
      this.tanggal = this.konsul[index].tanggal;
      this.diagnosis = this.konsul[index].diagnosis;
      this.obat = this.konsul[index].obat;
      this.catatan = this.konsul[index].catatan;
      let dt = this.monthChange(this.tanggal.slice(4, 8).replace(/\s/g, ""));
      this.tanggal =
        this.tanggal.slice(8, 11) + dt + this.tanggal.slice(10, 16);
      console.log(this.tanggal);
    },
    monthChange(month) {
      let name;

      switch (month) {
        case "Jan":
          name = "Januari";
          break;
        case "Feb":
          name = "Februari";
          break;
        case "Mar":
          name = "Maret";
          break;
        case "Apr":
          name = "April";
          break;
        case "May":
          name = "Mei";
          break;
        case "Jun":
          name = "Juni";
          break;
        case "Jul":
          name = "Juli";
          break;
        case "Aug":
          name = "Agustus";
          break;
        case "Sep":
          name = "September";
          break;
        case "Oct":
          name = "Oktober";
          break;
        case "Nov":
          name = "September";
          break;
        case "Dec":
          name = "Desember";
          break;
        default:
          name = month;
      }
      return name;
    },
  },
};
</script>

<style scoped src="../../public/styles/hasil_pemeriksaan.css">
</style>