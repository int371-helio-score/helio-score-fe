<template>
  <div>
    <div class="bg-light min-h-screen max-w-full">
      <navTeacher />
      <div class="inline-flex">
        <sidebarTeacher class="absolute z-40" />

        <div class="data relative">
          <div class="md:mr-10 divide-y divide-gray10">
            <div class="title flex">
              <div>
                <router-link to="/">หน้าหลัก</router-link>
              </div>
              <div class="mx-1 sm:mx-2">></div>
              <div>{{ subjectName }} ชั้นมัธยมศึกษาปีที่ {{ grade }}</div>
              <div class="mx-1 sm:mx-2">></div>
              <div>ห้อง {{ room }}</div>
            </div>

            <div
              class="my-5 md:grid-cols-6 grid-cols-1 grid text-secondary"
            >
              <button
                @click="clickStdList()"
                class="add md:block hidden focus:bg-babyblue"
              >
                <p class="hidden sm:block cursor-pointer">รายชื่อทั้งหมด</p>
              </button>

              <div class="md:col-span-4 md:col-end-7" v-show="list">
                <div class="grid md:grid-cols-3 gap-4 xl:gap-4 md:gap-2 pt-3">
                  <button
                    class="add md:block hidden focus:bg-babyblue"
                    @click="clickUploadStd()"
                  >
                    <div
                      class="flex justify-center items-center self-center md:text-xs lg:text-sm"
                    >
                      <span class="material-symbols-outlined mr-2">
                        group_add
                      </span>
                      <div>อัปโหลดรายชื่อ</div>
                    </div>
                  </button>

                  <button
                    class="add md:block hidden focus:bg-babyblue"
                    @click="clickUpload()"
                  >
                    <div
                      class="flex justify-center items-center self-center md:text-xs lg:text-sm"
                    >
                      <span class="material-symbols-outlined mr-2">
                        upload_file
                      </span>
                      <div>อัปโหลดคะแนน</div>
                    </div>
                  </button>

                  <button
                    class="add focus:bg-babyblue"
                    @click="clickAnnounce()"
                  >
                    <div
                      class="flex justify-center items-center self-center text-sm md:text-xs lg:text-sm"
                    >
                      <span class="material-symbols-outlined mr-2">
                        pending_actions </span
                      >คะแนนที่รอประกาศ
                    </div>
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- รอประกาศคะแนน -->
          <div v-if="announce">
            <div class="announce">รายการที่ยังไม่ได้ประกาศ</div>
            <div class="grid lg:grid-cols-4 md:grid-cols-2 mx-10 gap-4">
              <div
                class="content border-2 rounded-lg md:w-auto md:h-auto bg-white px-10 pt-10 pb-2 text-sm text-center"
                v-for="assign in toPublish"
                :key="assign.id"
              >
                <div class="stitle mb-5">ชื่องาน : {{ assign.title }}</div>
                <div class="flex justify-center">
                  <button
                    class="ojb bg-babyblue text-primary click"
                    @click="sentPublish(assign.id)"
                  >
                    ประกาศ
                  </button>
                </div>
              </div>
            </div>

            <div class="announce mt-10">ยังไม่ได้แจ้งเตือนไปยัง email</div>
            <div class="grid lg:grid-cols-4 md:grid-cols-2 mx-10 gap-4">
              <div
                class="content border-2 rounded-lg md:w-auto md:h-auto bg-white px-10 pt-10 pb-2 text-sm text-center"
                v-for="list in toAnnounce"
                :key="list.id"
              >
                <div class="stitle mb-5">ชื่องาน : {{ list.title }}</div>
                <div class="flex justify-center">
                  <button
                    class="ojb bg-babyblue text-primary click"
                    @click="sentEmail(list.id)"
                  >
                    ประกาศ
                  </button>
                </div>
              </div>
            </div>
          </div>

          <!-- อัปโหลดคะแนน -->

          <div v-if="uploadFile">
            <div class="lg:mr-10 md:pr-10">
              <div class="xl:mx-60 lg:mx-36">
                <div class="wrapper flex justify-center lg:mt-14 lg:pt-5">
                  <div class="grid grid-rows-3 xl:mx-20 lg:mx-8">
                    <div
                      class="text-gray100 lg:text-sm xl:text-base text-center"
                    >
                      <p class="flex justify-center">
                        จำเป็นต้องใช้เทมเพลตของ Helio score เท่านั้น
                      </p>
                      <p class="flex justify-center">
                        หากใช้เทมเพลตนอกเหนือจากที่กำหนดไว้
                        จะไม่สามารถทำการอัปโหลดไฟล์ได้
                      </p>
                    </div>
                    <div
                      class="font-bold text-secondary flex justify-center align-centerc items-center cursor-pointer lg:text-sm xl:text-base"
                    >
                      <p @click="downloadTemp(this.class_id)">
                        ดาวน์โหลดไฟล์เทมเพลตคะแนน
                      </p>
                    </div>
                    <div
                      class="flex justify-center text-center text-gray100 lg:text-sm xl:text-base"
                    >
                      <p class="">
                        เมื่อกรอกข้อมูลครบถ้วน บันทึกสกุลไฟล์เป็น .xlsx หรือ
                        .csv-UTF8 เท่านั้น
                      </p>
                    </div>
                  </div>
                </div>

                <div class="mt-2">
                  <div class="flex justify-center">
                    <div
                      id="yourBtn"
                      class="text-secondary"
                      @click="chooseFile()"
                    >
                      เลือกไฟล์คะแนน
                    </div>
                    <input
                      id="upFile"
                      style="height: 0px; width: 0px; overflow: hidden"
                      class="wrapper flex justify-content-center align-items-center"
                      type="file"
                      ref="file"
                      @change="handleFileUpload()"
                    />
                  </div>
                </div>
                <div
                  class="flex justify-center mt-2 text-gray100"
                  v-show="this.file != 0"
                >
                  <span
                    class="material-symbols-outlined"
                    style="color: #797979"
                  >
                    description
                  </span>
                  <p>{{ this.file.name }}</p>
                </div>

                <div class="flex gap-10 justify-center mt-8">
                  <div
                    class="flex justify-center ojb bg-white text-primary md:rounded-lg"
                  >
                    <button
                      class="md:w-32 h-12 text-sm"
                      id="custom-btn"
                      @click="clickUpload()"
                    >
                      ยกเลิก
                    </button>
                  </div>
                  <div class="flex justify-center ojb">
                    <button
                      class="md:w-32 h-12 text-sm bg-primary text-white md:rounded-lg"
                      @click="submitFile()"
                    >
                      อัปโหลดคะแนน
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- อัปโหลดรายชื่อ -->
          <div v-if="uploadStd">
            <div class="lg:mr-10 md:pr-10">
              <diV class="xl:mx-60 lg:mx-36">
                <div class="wrapper flex justify-center lg:mt-14 lg:pt-5">
                  <div class="grid grid-rows-3 xl:mx-20 lg:mx-8">
                    <div
                      class="text-gray100 lg:text-sm xl:text-base text-center"
                    >
                      <p class="flex justify-center">
                        จำเป็นต้องใช้เทมเพลตของ Helio score เท่านั้น
                      </p>
                      <p class="flex justify-center">
                        หากใช้เทมเพลตนอกเหนือจากที่กำหนดไว้
                        จะไม่สามารถทำการอัปโหลดไฟล์ได้
                      </p>
                    </div>
                    <div
                      class="font-bold text-secondary flex justify-center align-centerc items-center cursor-pointer lg:text-sm xl:text-base"
                    >
                      <p @click="downloadTempStd()">
                        ดาวน์โหลดไฟล์เทมเพลตรายชื่อ
                      </p>
                    </div>
                    <div
                      class="flex justify-center text-center text-gray100 lg:text-sm xl:text-base"
                    >
                      <p class="">
                        เมื่อกรอกข้อมูลครบถ้วน บันทึกสกุลไฟล์เป็น .xlsx หรือ
                        .csv-UTF8 เท่านั้น
                      </p>
                    </div>
                  </div>
                </div>

                <div class="mt-2">
                  <div class="flex justify-center">
                    <div
                      id="yourBtn"
                      class="text-secondary"
                      @click="chooseStdFile()"
                    >
                      เลือกไฟล์รายชื่อ
                    </div>
                    <input
                      id="upStdFile"
                      style="height: 0px; width: 0px; overflow: hidden"
                      class="flex justify-content-center align-items-center"
                      type="file"
                      ref="file"
                      @change="handleFileStd()"
                    />
                  </div>

                  <div
                    class="flex justify-center mt-2 text-gray100"
                    v-show="this.fileStd != 0"
                  >
                    <span
                      class="material-symbols-outlined"
                      style="color: #797979"
                    >
                      description
                    </span>
                    <p>{{ this.fileStd.name }}</p>
                  </div>
                </div>
              </diV>
            </div>

            <div
              class="flex gap-8 sm:gap-4 lg:gap-10 justify-center lg:mt-8 mt-20 sm:mt-32 lg:mr-10 md:pr-10"
            >
              <div class="flex justify-center ojb bg-light text-primary">
                <button
                  class="w-full h-8 px-4 sm:w-28 sm:h-10 md:w-36 lg:h-12 text-sm bg-white text-primary md:rounded-lg sm:rounded-md rounded"
                  @click="clickUploadStd()"
                >
                  ยกเลิก
                </button>
              </div>
              <div class="flex justify-center ojb">
                <button
                  class="w-full h-8 px-4 sm:w-28 sm:h-10 md:w-36 lg:h-12 text-sm bg-primary text-white md:rounded-lg sm:rounded-md rounded"
                  @click="checkFileStd()"
                >
                  อัปโหลดรายชื่อ
                </button>
              </div>
            </div>
          </div>

          <!-- Score List -->
          <div
            v-if="
              (uploadFile == false) & (announce == false) & (uploadStd == false)
            "
            class="md:mr-10 text-secondary"
            :class="{ 'overflow-x-auto': scroll }"
          >
            <div class="border grid grid-cols-4 bg-white rounded-md boardcash">
              <div class="box">
                <p class="head">คะแนนเต็ม</p>
                <p class="total text-alert">{{ this.stat.totalScore }}</p>
              </div>

              <div class="box">
                <p class="head">คะแนนสูงสุด</p>
                <p class="number">{{ this.stat.max }}</p>
              </div>

              <div class="box">
                <p class="head">คะแนนต่ำสุด</p>
                <p class="number">{{ this.stat.min }}</p>
              </div>

              <div class="box">
                <p class="head">คะแนนเฉลี่ย</p>
                <p class="number">{{ this.stat.average }}</p>
              </div>
            </div>

            <table class="h-full rounded-md text-sm overflow-x-auto">
              <tr class="bg-babyblue p-4 cursor-default">
                <th class="px-2">เลขที่</th>
                <th>รหัส</th>
                <th>ชื่อ-นามสกุล</th>
                <th>E-mail</th>
                <th v-for="tt in std" :key="tt._id" class="px-2">
                  {{ tt.title }}
                  <div
                    class="flex justify-center self-center items-center"
                    v-show="tt.total >= 1"
                  >
                    <p class="text-xs font-extralight">{{ tt.total }} คะแนน</p>
                    <span
                      class="material-symbols-outlined cursor-pointer ml-2"
                      @click="showEdit(tt)"
                      v-show="tt.owner && list"
                    >
                      edit_note
                    </span>
                  </div>
                </th>
                <th>คะแนนสะสม</th>
              </tr>

              <tr
                v-for="list in stdScore"
                :key="list.no"
                class="font-light bg-white hover:bg-gray10"
              >
                <td>
                  <div class="flex justify-center">{{ list.no }}</div>
                </td>
                <td>
                  <div class="flex justify-center">
                    {{ list.studentId }}
                  </div>
                </td>
                <td>
                  <div class="flex justify-center">
                    {{ list.firstName }} &nbsp;&nbsp;
                    {{ list.lastName }}
                  </div>
                </td>
                <td>
                  <div class="flex justify-center">
                    <p>
                      {{ list.email }}
                    </p>
                  </div>
                </td>
                <th
                  v-for="(s, index) in list.score"
                  :key="index"
                  class="font-light"
                >
                  {{ s }}
                </th>
                <th>
                  <div class="flex justify-center text-secondary">
                    {{ list.total }}
                  </div>
                </th>
              </tr>
              <!-- <tr class="font-light bg-white">ไม่มีคะแนน</tr> -->
            </table>

            <!-- Manage Score -->
            <div class="flex justify-end lg:mt-5">
              <div
                class="grid grid-cols-3 absolute bottom-0"
                v-show="stdScore.length > 0"
              >
                <div class="deleteBtn">
                  <span
                    class="material-symbols-outlined"
                    style="color: #797979"
                  >
                    delete
                  </span>
                  <div v-show="showList">
                    <p
                      class="text-sm hover:text-secondary cursor-pointer"
                      @click="showDelete()"
                    >
                      ลบคะแนน
                    </p>
                  </div>
                </div>

                <div class="deleteBtn">
                  <span
                    class="material-symbols-outlined"
                    style="color: #797979"
                  >
                    delete
                  </span>
                  <div v-show="showStd">
                    <p
                      class="text-sm hover:text-secondary cursor-pointer"
                      @click="showDeleteStd()"
                    >
                      ลบรายชื่อ
                    </p>
                  </div>
                </div>

                <div class="deleteBtn">
                  <span
                    class="material-symbols-outlined"
                    style="color: #797979"
                  >
                    delete
                  </span>
                  <div>
                    <p
                      class="text-sm hover:text-secondary cursor-pointer"
                      @click="deleteAllStudent()"
                    >
                      ลบรายชื่อทั้งหมด
                    </p>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Delete Score -->
    <div name="modal" v-show="deleteModal == true" v-if="editModal == false">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container relative">
            <img
              src="../../src/assets/Background.png"
              class="w-full relative"
            />

            <div>
              <div class="mx-20 mt-10">
                <p class="text-secondary font-bold my-2">ชิ้นงาน</p>
                <div>
                  <div
                    v-for="tt in std"
                    :key="tt._id"
                    class="flex justify-between mt-3"
                  >
                    <div class="flex justify-start">
                      {{ tt.title }}
                    </div>
                    <button
                      class="flex justify-end text-gray-600 hover:text-red-500"
                      @click="deleteAssignment(tt._id, tt.title)"
                    >
                      ลบ
                    </button>
                  </div>
                </div>
              </div>
            </div>

            <!-- button -->
            <div class="flex justify-center my-5 mt-12">
              <div class="bottom-8 gap-4 place-content-end">
                <button
                  class="bg-light text-secondary2 border border-secondary2 rounded-md px-6 py-1 ml-2"
                  @click="deleteModal = false"
                >
                  ออก
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Delete Student -->
    <div name="modal" v-show="deleteStdModal == true" v-if="editModal == false">
      <div class="modal-mask">
        <div class="modal-wrapper">
          <div class="modal-container relative">
            <img
              src="../../src/assets/Background.png"
              class="w-full relative"
            />

            <div>
              <div class="mx-20 mt-10">
                <p class="text-secondary font-bold my-2 flex justify-center">
                  รายชื่อ
                </p>
                <div>
                  <div
                    v-for="list in stdScore"
                    :key="list.no"
                    class="flex justify-between mt-3"
                  >
                    <!-- {{ std }} -->
                    <div>
                      <p>เลขที่ {{ list.no }}</p>
                    </div>
                    <div class="flex justify-start">
                      {{ list.firstName + " " + list.lastName }}
                    </div>
                    <button
                      class="flex justify-end text-gray-600 hover:text-red-500"
                      @click="
                        deleteStudent(
                          list.studentId,
                          list.firstName,
                          list.lastName
                        )
                      "
                    >
                      ลบ
                    </button>
                  </div>
                </div>
              </div>
            </div>

            <!-- button -->
            <div class="flex justify-center my-5 mt-12">
              <div class="bottom-8 gap-4 place-content-end">
                <button
                  class="bg-light text-secondary2 border border-secondary2 rounded-md px-6 py-1 ml-2"
                  @click="deleteStdModal = false"
                >
                  ออก
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div v-if="this.loadingScr">
    <loadingScreen></loadingScreen>
  </div>

  <!-- Edit Score -->

  <editScore
    v-if="editModal"
    :scoreComp="editScore"
    @showEdit="showEdit"
    class="absolute"
  >
  </editScore>
</template>

<script>
import axios from "axios";
import SidebarTeacher from "@/components/SidebarTeacher.vue";

export default {
  components: { SidebarTeacher },
  name: "StudentList",
  props: ["classId", "subjectName", "subId", "group"],
  data() {
    return {
      url: "helio/score",
      template: "helio/score/template",
      templateStd: "helio/studentList/template",
      announceUrl: "helio/score/toPublish",
      sentEmailUrl: "api/helio/score/toAnnounce",
      importStd: "helio/studentList",
      sent: "helio/mail",
      checkOwner: "helio/class/owner",
      urlStat: "helio/class/stat",
      urlStd: "helio/studentList",
      stdList: [],
      stat: "",
      toAnnounce: [],
      toPublish: [],
      std: [],
      grade: null,
      room: null,
      class_id: null,
      subject_id: null,
      uploadFile: false,
      announce: false,
      file: "",
      fileStd: "",
      sentToEmail: "",
      stdScore: [],
      scroll: false,
      uploadStd: false,
      showAssignList: false,
      showStdList: false,
      deleteModal: false,
      deleteStdModal: false,
      editModal: false,
      editScore: null,
      list: false,
      announceStatus: false,
      checkHasRecord: "",
      loadingScr: false,
    };
  },

  methods: {
    showList() {
      this.showAssignList = true;
    },

    showStd() {
      this.showStdList = true;
    },

    chooseFile() {
      document.getElementById("upFile").click();
    },

    chooseStdFile() {
      document.getElementById("upStdFile").click();
    },

    handleFileUpload() {
      this.file = this.$refs.file.files[0];
    },

    handleFileStd() {
      this.fileStd = this.$refs.file.files[0];
    },

    submitFile() {
      let formData = new FormData();
      formData.append("file", this.file);
      formData.append("class_id", this.$route.query.class_id);

      axios
        .post(`${this.url}`, formData, {
          headers: {
            Authorization: localStorage.getItem("token"),
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          if (res.data.statusCode === 200 || res.status === 201) {
            this.getStudent(this.$route.query.class_id);
            this.getStat(this.$route.query.class_id);
            alert("อัปโหลดไฟล์สมบูรณ์ กด ประกาศคะแนน เพื่อประกาศ");
            this.file = "";
            this.getPublish(this.$route.query.class_id);
            this.clickAnnounce().$router.go();
          }
        });
    },

    checkFileStd() {
      axios
        .get(`helio/class/record/${this.class_id}`, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((res) => {
          this.checkHasRecord = res.data.data.results.hasRecord;
          if (this.checkHasRecord == false) {
            this.submitFileStd();
          } else {
            let text =
              "รายชื่อและคะแนนของนักเรียนในระบบที่ไม่ตรงกับไฟล์นี้จะ 'ถูกลบ' ต้องการดำเนินการหรือไม่?";
            if (confirm(text) == true) {
              return this.submitFileStd();
            }
            return;
          }
          return;
        });
    },

    submitFileStd() {
      let formData = new FormData();
      formData.set("file", this.fileStd);
      formData.set("classId", this.class_id);
      axios
        .post(`${this.importStd}`, formData, {
          headers: {
            Authorization: localStorage.getItem("token"),
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          if (res.data.message == "You cannot import your email as student.") {
            return alert(
              "ไม่สามารถอัปโหลดรายชื่อได้ เนื่องจากมีอีเมลของคุณอยู่ในรายชื่อ"
            );
          }
          if (res.data.statusCode === 200) {
            (this.fileStd = ""), alert("อัปโหลดราชื่อ สำเร็จ");
            this.$router.go();
          }
        })
        .catch((err) => {
          console.log(err);
        });
    },

    clickStdList() {
      this.uploadStd = false;
      this.announce = false;
      this.uploadFile = false;
    },

    clickUploadStd() {
      this.uploadStd = !this.uploadStd;
      this.announce = false;
      this.uploadFile = false;
      this.fileStd = 0;
    },

    clickUpload() {
      this.uploadFile = !this.uploadFile;
      this.announce = false;
      this.uploadStd = false;
    },

    clickAnnounce() {
      this.announce = !this.announce;
      this.uploadFile = false;
      this.uploadStd = false;
    },

    downloadTemp(classId) {
      axios
        .get(`${this.template}/${classId}`, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
          responseType: "blob",
        })
        .then((response) => {
          window.URL = window.webkitURL || window.URL;
          const contentType =
            "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
          const csvFile = new Blob([response.data], { type: contentType });
          const downloadURL = window.URL.createObjectURL(csvFile);
          const downloadLink = document.createElement("a");
          downloadLink.href = downloadURL;
          const fileName = response.headers["content-disposition"]
            .split(";")[1]
            .split("=")[1]
            .replace('"', "")
            .replace('"', "");
          downloadLink.setAttribute("download", decodeURI(fileName));
          document.body.appendChild(downloadLink);
          downloadLink.click();
        });
    },

    downloadTempStd() {
      axios
        .get(this.templateStd, {
          responseType: "blob",
        })
        .then((response) => {
          window.URL = window.webkitURL || window.URL;
          const contentType =
            "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet";
          const csvFile = new Blob([response.data], { type: contentType });
          const downloadURL = window.URL.createObjectURL(csvFile);
          const downloadLink = document.createElement("a");
          downloadLink.href = downloadURL;
          const fileName = response.headers["content-disposition"]
            .split(";")[1]
            .split("=")[1]
            .replace('"', "")
            .replace('"', "");
          downloadLink.setAttribute("download", fileName);
          document.body.appendChild(downloadLink);
          downloadLink.click();
        });
    },

    async getStudent(classId) {
      try {
        this.stdScore = [];
        axios
          .get(`${this.url}/${classId}`, {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          })
          .then((response) => {
            this.std = response.data.data.results;
            if (this.std[0].owner) {
              this.list = true;
            }
            for (const s of this.std) {
              s.scores.forEach((each) => {
                let dup = this.stdScore.filter((el) => {
                  return el.no === each.no;
                });

                if (dup.length == 0) {
                  const obj = {
                    no: each.no,
                    studentId: each.studentId,
                    title: each.title,
                    firstName: each.firstName,
                    lastName: each.lastName,
                    email: each.email,
                    score: [],
                    total: 0,
                  };
                  obj.score.push(each.score);
                  this.stdScore.push(obj);
                } else {
                  this.stdScore
                    .find((x) => x.no === each.no)
                    .score.push(each.score);
                }
              });
            }
            for (const each of this.stdScore) {
              for (const s of each.score) {
                if (!isNaN(Number(s))) {
                  each.total += Number(s);
                }
              }
            }
            if (this.std.length > 5) {
              this.scroll = true;
            }
            return response.data.data.results;
          });
      } catch (error) {
        console.log(`Could not get! ${error}`);
      }
    },

    getStat(classId) {
      axios
        .get(`${this.urlStat}/${classId}`, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((res) => {
          this.stat = res.data.data.results;
          return;
        });
    },

    owner(classId) {
      axios
        .get(`${this.checkOwner}/${classId}`, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((response) => {
          this.list = response.data.data.results.owner;
          return;
        });
    },

    async getPublish(classId) {
      try {
        axios
          .get(`helio/score/toPublish/${classId}`, {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          })
          .then((response) => {
            this.toPublish = response.data.data.results;
            return response.data.data.results;
          });
      } catch (error) {
        console.log(`Could not get! ${error}`);
      }
    },

    sentPublish(id) {
      let text = "ต้องการส่งคะแนนไปยัง Email นักเรียนหรือไม่?";
      const choose = confirm(text);
      this.loadingScr = true;
      axios
        .patch(
          `helio/score/publish`,
          {
            score_id: id,
            announce: choose,
          },
          {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          }
        )
        .then((res) => {
          if (res.data.statusCode === 200 || res.status == 200) {
            this.loadingScr = false;
            this.$router.go();
          }
        });
    },

    async getSentToEmail(classId) {
      try {
        axios
          .get(`helio/score/toAnnounce/${classId}`, {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          })
          .then((response) => {
            this.toAnnounce = response.data.data.results;
            return response.data.data.results;
          });
      } catch (error) {
        console.log(`Could not get! ${error}`);
      }
    },

    sentEmail(id) {
      this.loadingScr = true;
      axios
        .post(
          `helio/mail`,
          { scoreId: id },
          {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          }
        )
        .then((res) => {
          if (res.data.statusCode === 200) {
            this.loadingScr = false;
            alert("ส่งคะแนนไปยัง Email สำเร็จ");
            this.$router.go();
          }
        });
    },

    showDeleteStd() {
      this.deleteStdModal = true;
    },

    deleteStudent(stdId, firstName, lastName) {
      let text = "ต้องการลบ " + firstName + " " + lastName + " หรือไม่";
      if (confirm(text) == true) {
        axios
          .post(
            "helio/studentList/deleteStudent",
            { classId: this.class_id, studentId: stdId },
            {
              headers: {
                Authorization: localStorage.getItem("token"),
              },
            }
          )
          .then((res) => {
            if (res.data.statusCode === 200) {
              alert("ลบ" + firstName + " " + lastName + "สำเร็จ");
              this.$router.go();
            }
          })
          .catch((err) => {
            alert(err);
          });
      } else {
        return;
      }
    },

    deleteAllStudent() {
      let text = "ต้องการลบรายชื่อทั้งหมดหรือไม่?";
      if (confirm(text) == true) {
        axios
          .delete(`helio/studentList/${this.class_id}`, {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          })
          .then((res) => {
            if (res.data.statusCode === 200) {
              alert("ลบสำเสร็จ");
              this.$router.go();
            }
          })
          .catch((err) => {
            alert(err.response.message);
          });
      }
    },

    showDelete() {
      this.deleteModal = true;
    },

    async deleteAssignment(score_id, title) {
      let text = "ต้องการลบชิ้นงาน " + title + " หรือไม่";
      if (confirm(text) == true) {
        axios
          .delete(`helio/score/${score_id}`, {
            headers: {
              Authorization: localStorage.getItem("token"),
            },
          })
          .then((res) => {
            if (res.data.statusCode === 200) {
              this.$router.go();
            }
          })
          .catch((err) => {
            alert(err.response.message);
          });
      } else {
        return;
      }
    },

    async showEdit(scoreList) {
      this.editScore = scoreList;
      this.editModal = !this.editModal;
      if (this.editModal == false) {
        await this.getStudent(this.$route.query.class_id);
      }
    },

    async submitEdit() {
      var data = {
        firstName: this.newFirstName,
        lastName: this.newLastName,
      };
      // if(editImage){
      //   data.image = this.inputFile;
      // }
      axios
        .patch("helio/score", data, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((res) => {
          if (res.data.statusCode === 200) {
            alert("Edit success");
            this.showModal = false;
            this.edit = false;
            this.getAccount();
            this.newFirstName = "";
            this.newLastName = "";
          }
        })
        .catch((err) => {
          alert(err.response.data.message);
        });
    },

    getAllStudentList() {
      axios
        .get(this.urlStd, {
          headers: {
            Authorization: localStorage.getItem("token"),
          },
        })
        .then((res) => {
          if (res.data.statusCode === 200) {
            this.stdList = res.data.data.results;
          }
        });
    },
  },

  async created() {
    this.grade = this.$route.params.grade;
    this.room = this.$route.query.room;
    this.class_id = this.$route.query.class_id;
    this.subject_id = this.$route.params.subId;

    await this.getStudent(this.$route.query.class_id);
    await this.getPublish(this.$route.query.class_id);
    await this.getSentToEmail(this.$route.query.class_id);
    await this.owner(this.$route.query.class_id);
    await this.getStat(this.$route.query.class_id);
    await this.getAllStudentList();
  },
};
</script>

<style scoped>
span {
  color: #42a5f5;
  @apply xl:mr-2 md:text-xs lg:text-xl;
}
.buttom {
  border-radius: 0px 10px 0px 12px;
}
th {
  @apply md:py-4;
}
.container {
  height: 400px;
  width: 800px;
  position: relative;
}
.wrapper {
  /* position: relative; */
  height: 100%;
  width: 100%;
  border-radius: 10px;
  background: #fff;
  border: 1px solid #b3dbfb;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.click {
  @apply cursor-pointer rounded-md
  md:w-36 md:mb-4 lg:w-36;
}
.data {
  @apply w-screen px-5 pb-16
  sm:px-10 sm:pt-8 sm:pb-16
  md:pt-0 md:pl-10 mt-20 md:px-0
  lg:pl-60 lg:mt-24 lg:pb-10;
}
.title {
  @apply text-sm font-medium mt-5 text-secondary
  sm:text-base sm:font-bold
  lg:text-xl lg:font-bold
  md:mt-10 md:text-lg md:font-bold;
}
.dropzone {
  position: absolute;
  z-index: 1;
  box-sizing: border-box;
  display: table;
  table-layout: fixed;
  width: 100px;
  height: 80px;
  top: 86px;
  left: 100px;
  border: 1px dashed #a4a4a4;
  border-radius: 3px;
  text-align: center;
  overflow: hidden;
}

.content {
  display: table-cell;
  vertical-align: middle;
}

.upload {
  margin: 6px 0 0 2px;
}

.filename {
  display: block;
  color: #676767;
  font-size: 14px;
  line-height: 18px;
}
.input {
  position: absolute;
  display: flex;
  width: 100;
  height: 100;
}
.add {
  background: white;
  box-shadow: 0px 1px 5px rgba(214, 214, 214, 0.5);
  /* border-radius: 22px; */
  @apply md:px-8 py-1 rounded md:rounded-full;
}

table {
  border-collapse: collapse;

  @apply min-w-full;
  /* border-radius: 100px; */
}

table td {
  @apply py-2 px-2;
}
.tab {
  /* width: 90rem; */
  @apply w-screen;
}
table::-webkit-scrollbar {
  scrollbar-color: rgba(255, 100, 70, 0.8) rgba(0, 100, 200, 0.5);
}
table::-webkit-scrollbar-thumb {
  scrollbar-color: rgba(255, 100, 70, 0.8) rgba(0, 100, 200, 0.5);
}
table::-webkit-scrollbar-thumb:window-inactive {
  scrollbar-color: rgba(255, 100, 70, 0.8) rgba(0, 100, 200, 0.5);
}
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  width: 721px;
  height: 531px;
  margin: 0px auto;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  border-radius: 25px;
  overflow-y: auto;
  @apply w-3/4 md:w-96 lg:w-1/2;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
  margin: 20px 0;
}

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}
.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
.boardcash {
  @apply mb-2 py-4 md:mb-5 lg:py-5;
}
.box {
  @apply justify-center
  md:text-sm text-xs;
}
.number {
  @apply font-bold flex justify-center text-secondary
  lg:text-xl;
}
.total {
  @apply font-bold flex justify-center text-alert text-lg
  md:text-xl;
}
.head {
  @apply flex justify-center text-xs text-secondary;
}
.announce {
  @apply mx-10 text-secondary text-sm pb-2;
}
#yourBtn {
  /* position: relative; */
  top: 150px;
  /* font-family: calibri; */
  /* width: ; */
  padding: 10px;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border: 1px solid #b3dbfb;
  text-align: center;
  background-color: #dadada;
  cursor: pointer;
  @apply w-full;
}
.cell-note {
  position: relative;
}
.cell-note:after {
  content: "";
  position: absolute;
  display: block;
  top: 0;
  right: 0;
  border-left: 7px solid transparent;
  border-top: 7px solid red;
}
.deleteBtn {
  @apply mt-5 lg:mt-10 flex justify-end sm:mx-0 md:mx-5 items-center self-center text-gray100;
}
</style>
