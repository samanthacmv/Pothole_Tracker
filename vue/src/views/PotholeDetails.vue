<template>
  <div class="page">
    <div>
      <div class="inspection-form pothole-details">
        <h2>{{ pothole.address }}</h2>
        <div class="info">
          <h1 class="details">Description:</h1>
          <h1 class="potholeInfo">{{ pothole.description }}</h1>
          <h1 class="details">Other Location Information:</h1>
          <h1 class="potholeInfo">{{ pothole.locationDetails }}</h1>

          <h1 class="details">Severity:</h1>
          <h1 class="potholeInfo">
            <select id="severity" class="dropdown" v-model="pothole.severity">
              <option value="1">1 - High</option>
              <option value="2">2 - Medium</option>
              <option value="3">3 - Low</option>
            </select>
          </h1>

          <h1 class="details">Date Reported:</h1>
          <h1 class="potholeInfo">{{ pothole.reportedDate }}</h1>

          <h1 class="details">Inspected Date:</h1>
          <h1 class="potholeInfo">
            <input
              type="date"
              id="inspectionDate"
              v-model="pothole.inspectedDate"
            />
          </h1>

          <h1 class="details">Repair Date:</h1>
          <h1 class="potholeInfo">
            <input type="date" id="repairDate" v-model="pothole.repairDate" />
          </h1>

          <h1 v-if="pothole.canContact" class="details">
            Contact Phone Number:
          </h1>
          <h1 v-if="pothole.canContact" class="potholeInfo">
            {{ pothole.reporter.phoneNumber }}
          </h1>
        </div>
        <form>
          <div class="checkbox-group">
            <div>
              <label for="inspected">Inspected</label>
              <input
                type="checkbox"
                id="inspected"
                v-model="pothole.inspected"
              />
            </div>
            <div>
              <label for="repaired">Repaired</label>
              <input type="checkbox" id="repaired" v-model="pothole.repaired" />
            </div>
          </div>

          <div class="button-group">
            <button class="submitBtn" @click.prevent="updatePothole()">
              Submit
            </button>
            <button class="btnDelete" @click.prevent="deletePothole()">
              Delete
            </button>
            <button
              id="upload_widget"
              class="cloudinary-button"
              @click.prevent="openUploadWidget()"
            >
              Upload Picture
            </button>
          </div>
        </form>
      </div>
    </div>
    <div class="picture-group" v-if="pothole.imgUrl != null">
      <img v-bind:src="pothole.imgUrl" alt="No picture available" />
    </div>
  </div>
</template>

<script>
import PotholeService from "../services/PotholeService";

export default {
  name: "pothole-details",
  data() {
    return {
      pothole: {},
    };
  },
  created() {
    const potholeId = this.$route.params.id;
    PotholeService.getPothole(potholeId)
      .then((response) => {
        this.$store.commit("SET_ACTIVE_POTHOLE", response.data);
        this.pothole = response.data;
      })
      .catch((error) => {
        if (error.response.status == 404) {
          this.$router.push({ name: "NotFound" });
        }
      });
  },
  methods: {
    updatePothole() {
      if (this.isUpdateValid()) {
        alert("Submitting");
        PotholeService.updatePothole(this.pothole)
          .then((response) => {
            if (response.status == 200) {
              this.$router.push({ name: "home" });
            }
          })
          .catch((error) => console.error(error));
      } else {
        alert(
          "Invalid update. Please make sure that there is a date for inspected and reported if they are checked. Also make sure that repair date is after inspected date."
        );
      }
    },
    isUpdateValid() {
      if (this.pothole.inspected && !this.pothole.inspectedDate) {
        return false;
      }
      if (this.pothole.repaired && !this.pothole.repairDate) {
        return false;
      }
      if (this.pothole.inspectedDate && this.pothole.repairDate) {
        const inspectedDate = new Date(this.pothole.inspectedDate);
        const repairDate = new Date(this.pothole.repairDate);
        if (repairDate <= inspectedDate) {
          return false;
        }
      }
      // Add other constraint checks here if needed
      return true;
    },
    openUploadWidget() {
      const widget = window.cloudinary.createUploadWidget(
        {
          cloud_name: "dd6n4zinx",
          upload_preset: "ryc8jlxd",
        },
        (error, result) => {
          if (!error && result && result.event === "success") {
            console.log("Done Uploading Image", result.info);
            this.pothole.imgUrl = result.info.secure_url;
          }
        }
      );
      widget.open();
    },
    deletePothole() {
      alert("Deleting");
      PotholeService.deletePothole(this.pothole.id).then((response) => {
        if (response.status === 200) {
          this.$router.push({ name: "home" });
        }
      });
    },
  },
};
</script>


<style scoped>
form {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}
.page {
  display: flex;
  margin: auto;
  justify-content: space-evenly;
}
img {
  max-width: 40vw;
  height: auto;
}
h1 {
  color: white;
}
.inspection-form {
  width: 35vw;
  max-width: 80vw;
  margin: 30px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 8px;
  background-color: rgba(128, 128, 128, 0.822);
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}
.details,
.potholeInfo {
  background-color: transparent;
  font-size: 20px;
  max-width: 100%;
  text-align: left;
}
.info {
  display: grid;
  grid-template-columns: 1fr 1fr;
  margin-top: -25px;
}

label {
  font-size: 20px;
  color: white;
  font-family: "Oxygen", sans-serif;
  font-weight: bold;
}

.form-title {
  font-size: 24px;
}

.form-container {
  margin-top: 20px;
}

.checkbox-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  margin-top: 20px;
  margin-bottom: 12px;
}

#checkbox {
  margin-right: 20px;
}

.checkbox-label {
  font-weight: bold;
}

.form-group {
  display: grid;
  grid-template-columns: 1fr 1fr;
  margin-bottom: 20px;
}

.button-group {
  display: flex;
  justify-content: space-between;
}

.submitBtn {
  background-color: #007bff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 16px;
}
.submitBtn:hover {
  background-color: #0056b3;
}

.btnDelete {
  background-color: #dc3545;
  color: #fff;
  border: none;
  padding: 10px 20px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 16px;
}
.btnDelete:hover {
  background-color: #ac2835;
  color: #fff;
}
.cloudinary-button {
  background-color: #43dc35;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 0 5px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 16px;
}
.cloudinary-button:hover {
  background-color: #298a20;
}
#severity {
  width: 100px;
  height: 35px;
}
#repairDate,
#inspectionDate {
  height: 35px;
}
h2 {
  font-size: 30px;
  text-align: center;
  top: -40px;
}
.picture-group {
  display: flex;
}
.uploaded-image {
  display: flex;
}
</style>