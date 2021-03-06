<template>
  <div class="user-profile-form">
    <v-toolbar
      absolute
      light
    >
      <v-layout
        class="top-bar"
        row
        align-center
      >
        <v-flex xs12>
          <user-avatar />
        </v-flex>
        <v-flex>
          <v-btn
            to="/"
            icon
            light
            nuxt >
            <v-icon>close</v-icon>
          </v-btn>
        </v-flex>
      </v-layout>
    </v-toolbar>

    <div class="form-container">
      <form class="form px-4 pt-4 pb-5">
        <v-text-field
          v-validate="'required|max:254'"
          v-model="profile.name"
          :counter="254"
          :error-messages="errors.collect('Name')"
          label="Name"
          data-vv-name="Name"
          required
          light
        />

        <v-select
          v-model="profile.type"
          :items="userTypes"
          label="Selecte usertype"
          item-text="verbose_name"
          item-value="id"
          item-disabled="disabled"
          light
        />

        <v-layout
          row
          class="switch-row email-group"
        >
          <v-flex>
            <v-text-field
              v-validate="'email'"
              v-model="profile.email"
              :error-messages="errors.collect('E-Mail')"
              label="E-mail"
              data-vv-name="E-Mail"
              light
            />
          </v-flex>

          <v-flex>
            <v-switch
              :label="publicEmailSwitchLabel"
              v-model="profile.public_email"
              light
              hide-details
              color="primary"
            />
          </v-flex>
        </v-layout>

        <div class="email-privacy-hint">
          <span v-show="profile.public_email">
            <v-icon small>info</v-icon>
            Your email will be visible in the map and in your user detail.</span>
          <span v-show="!profile.public_email">
            <v-icon small>info</v-icon>
            Your email will only be stored in the database and only displayed to you.</span>
        </div>

        <v-layout
          row
          class="switch-row"
        >
          <v-flex>
            <div class="switch-custom-label">
              Receive occasional news about conferences, VueJS meetups and job opportunities in my area
            </div>
          </v-flex>
          <v-flex>
            <v-switch
              :label="optInSwitchLabel"
              v-model="profile.news_opt_in"
              light
              hide-details
              color="primary"
            />
          </v-flex>
        </v-layout>

        <v-layout
          row
          class="switch-row"
        >
          <v-flex>
            <div class="switch-custom-label">
              Show my Github “Jobs profile” setting
            </div>
          </v-flex>
          <v-flex>
            <v-switch
              :label="hireableSwitchLabel"
              v-model="profile.show_hireable"
              light
              hide-details
              color="primary"
            />
          </v-flex>
        </v-layout>

        <v-text-field
          v-validate="'url'"
          v-if="false"
          v-model="profile.githubUrl"
          :error-messages="errors.collect('GitHub Profile')"
          label="GitHub profile"
          data-vv-name="GitHub Profile"
          light
        />

        <v-text-field
          v-validate="'url'"
          v-model="profile.twitter_url"
          :error-messages="errors.collect('Twitter profile')"
          label="Twitter profile"
          data-vv-name="Twitter profile"
          light
        />

        <v-text-field
          v-validate="'url'"
          v-model="profile.website_url"
          :error-messages="errors.collect('Your website')"
          label="Your website"
          data-vv-name="Your website"
          light
        />

        <v-text-field
          v-validate="'max:254'"
          v-model="profile.company"
          :counter="254"
          :error-messages="errors.collect('Organisation')"
          data-vv-name="Organisation"
          label="Organisation"
          light
        />

        <v-select
          v-model="profile.tags"
          :items="tagList"
          label="Tags"
          multiple
          tags
          chips
          deletable-chips
          light
        />

        <v-text-field
          v-validate="'max:500'"
          v-model="profile.bio"
          :error-messages="errors.collect('Bio')"
          :counter="500"
          data-vv-name="Bio"
          name="Bio"
          label="Bio"
          textarea
          light
        />

      </form>
    </div>
    <v-layout
      class="user-profile-actions"
      align-center
      row
    >
      <div class="last-saved caption">
        Last saved on: {{ lastModified }}
      </div>
      <v-spacer />
      <v-btn
        color="primary"
        class="ma-0"
        @click.stop="save">
        Save<span class="trim">&nbsp;changes</span>
      </v-btn>
    </v-layout>
  </div>
</template>

<script>
import {mapGetters, mapActions} from 'vuex';
import UserAvatar from './UserAvatar.vue';
import { format } from 'date-fns';

export default {
  name: 'UserProfileForm',
  components: {
    UserAvatar
  },
  data () {
    return {
      profile: {
        name: '',
        email: '',
        public_email: false,
        news_opt_in: false,
        show_hireable: false,
        twitter_url: '',
        website_url: '',
        company: '',
        tags: [],
        bio: '',
        modified: null
      }
    };
  },
  computed: {
    ...mapGetters({
      userProfile: 'user/getUserProfile',
      usLoggedIn: 'user/getLoginStatus',
      tagList: 'getTags',
      userTypes: 'getUserTypes'
    }),
    publicEmailSwitchLabel () {
      return this.profile.public_email ? 'public' : 'private';
    },
    optInSwitchLabel () {
      return this.profile.news_opt_in ? 'yes' : 'no';
    },
    hireableSwitchLabel () {
      return this.profile.show_hireable ? 'yes' : 'no';
    },
    lastModified () {
      return this.profile.modified ? format(this.profile.modified, 'YYYY-MM-DD HH:mm') : '';
    }
  },
  watch: {
    userProfile: {
      immediate: true,
      handler (p) {
        this.profile = {...p};
      }
    }
  },
  methods: {
    ...mapActions({
      updateUserProfile: 'user/updateUserProfile'
    }),
    async save () {
      await this.updateUserProfile(this.profile);
      this.$router.push('/');
    }
  }
};
</script>

<style lang="less">
  @import "../assets/style/variables.less";
  @import "../assets/style/mixins.less";

  .user-profile-form {
    height: 100%;
    border-radius: 3px;

    .toolbar {
      .toolbar__content {
        height: @map-card-height !important;
      }

      .top-bar {
        height: @map-card-height;
        padding: 0 12px;
        background-color: @color-white;
      }
    }

    .form-container {
      height: calc(100% - 80px);
      position: relative;
      top: @map-card-height;
      width: 100%;
      overflow-y: auto;
      padding-bottom: 48px;

      .switch-row {
        margin: 16px 0;

        .flex {
          &:first-child {
            flex-grow: 1;
          }

          &:last-child {
            min-width: 110px;
            max-width: 110px;
            padding-left: 16px;
          }
        }

        .switch-custom-label {
          margin-top: 7px;
          font-size: @font-size-tiny;
          color: @font-dark-primary;
        }

        &.email-group {
          margin: 0;

          .switch {
            padding-top: 18px;
          }
        }
      }

      .email-privacy-hint {
        font-size: @font-size-tiny;
        color: @font-dark-secondary;

        .icon {
          position: relative;
          top: -1px;
          color: @font-dark-disabled;
        }
      }
    }

    .user-profile-actions {
      position: absolute;
      bottom: 0;
      left: 0;
      right: 0;
      height: @map-card-height;
      padding: 0 12px;
      border-top: 1px solid @font-dark-dividers;
      background-color: @color-white;

      .last-saved {
        color: @font-dark-disabled;
      }

      .trim {
        display: inline-block;
      }
    }

    // Responsive
    .viewport-sm & {
      .trim {
        display: none;
      }
    }
  }
</style>
