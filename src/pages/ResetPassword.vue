<template>
  <v-container>
    <div>
       <h4 class = "display-1">Reset Password</h4>
       <instructions details="Reset your password on this nifty site."/>

       <v-form v-model="valid">
              <v-text-field
                v-model="resetPassword.email"
                v-bind:rules="rules.email"
                error-count="10"
                type="email"
                label="Your email address"
                required
              ></v-text-field>
              <v-text-field
                v-model="resetPassword.currentPassword"
                v-bind:rules="rules.password"
                type="password"
                label="Your current password"
                required
              ></v-text-field>
              <v-text-field
                v-model="resetPassword.newPassword"
                v-bind:rules="rules.password"
                error-count="10"
                type="password"
                label="Your new password"
                required
              ></v-text-field>
              <v-text-field
                v-model="resetPassword.password"
                v-bind:rules="rules.password"
                error-count="10"
                type="password"
                label="Confirm your new password"
                required
              ></v-text-field>
            <v-btn v-bind:disabled="!valid" v-on:click="handleSubmit">Reset Password</v-btn>
    </v-form>

<div class="text-xs-center">
        <v-dialog v-model="dialogVisible" width="500">
          <v-card>
            <v-card-title primary-title>
              {{ dialogHeader }}
            </v-card-title>

            <v-card-text>
              {{ dialogText }}
            </v-card-text>

            <v-divider></v-divider>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="primary" text v-on:click="hideDialog">Okay</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </div>
    </div>
  </v-container>

</template>

<script>

import Instructions from "../components/Instructions.vue";

export default {
  name: "ResetPasswordPage",
  components: {
    Instructions,
  },
  data: function () {
    return {
      valid: false,
      
      resetPassword: {
        email: "",
        currentPassword: "",
        newPassword: "",
        confirmPassword: "",
      },

      passwordReset: false,

      dialogHeader: "<no dialogHeader>",
      dialogText: "<no dialogText>",
      dialogVisible: false,

      rules: {
        required: [(val) => val.length > 0 || "Required"],
          email: [
            (val) => /\w{3,}@\w{3,}(?:.\w{3,})+$/.test(val) || "Invalid e-mail",
          ],
          password: [
            (val) => /[A-Z]/.test(val) || "Need upper case letter",
            (val) => /[a-z]/.test(val) || "Need lower case letter",
            (val) => /\d/.test(val) || "Need digit",
            (val) => val.length >= 8 || "Minimum 8 characters",
          ],
      },
    };
  },

  methods: {
    handleSubmit: function () {
      this.passwordReset = false;
      this.$axios
        .post("/accounts", {
          email: this.resetPassword.email,
          password: this.resetPassword.newPassword,
        })
        .then((result) => {
          if (result.data.ok) {
            this.showDialog("Success", result.data.msge);
            this.passwordReset = true;
          } else {
            this.showDialog("Sorry", result.data.msge);
          }
        })
        .catch((err) => this.showDialog("Failed", err));
    },

    showDialog: function (header, text) {
      this.dialogHeader = header;
      this.dialogText = text;
      this.dialogVisible = true;
    },

    hideDialog: function () {
      this.dialogVisible = false;
      if (this.passwordReset) {
        this.$router.push({name: "home-page"});
      }
    },
  },
};
</script>
