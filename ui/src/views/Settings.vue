<!--
  Copyright (C) 2022 Nethesis S.r.l.
  SPDX-License-Identifier: GPL-3.0-or-later
-->
<template>
  <cv-grid fullWidth>
    <cv-row>
      <cv-column class="page-title">
        <h2>{{ $t("settings.title") }}</h2>
      </cv-column>
    </cv-row>
    <cv-row v-if="error.getConfiguration">
      <cv-column>
        <NsInlineNotification
          kind="error"
          :title="$t('action.get-configuration')"
          :description="error.getConfiguration"
          :showCloseButton="false"
        />
      </cv-column>
    </cv-row>
    <cv-row>
      <cv-column>
        <cv-tile light>
          <cv-form @submit.prevent="configureModule">
            <!-- Basic Settings -->
            <div class="section-title">{{ $t("settings.basic_settings") }}</div>
            <cv-text-input
              :label="$t('settings.documenso_fqdn')"
              placeholder="documenso.example.org"
              v-model.trim="host"
              class="mg-bottom"
              :invalid-message="$t(error.host)"
              :disabled="loading.getConfiguration || loading.configureModule"
              ref="host"
            >
            </cv-text-input>
            <cv-toggle
              value="letsEncrypt"
              :label="$t('settings.lets_encrypt')"
              v-model="isLetsEncryptEnabled"
              :disabled="loading.getConfiguration || loading.configureModule"
              class="mg-bottom"
            >
              <template slot="text-left">{{
                $t("settings.disabled")
              }}</template>
              <template slot="text-right">{{
                $t("settings.enabled")
              }}</template>
            </cv-toggle>
            <cv-toggle
              value="httpToHttps"
              :label="$t('settings.http_to_https')"
              v-model="isHttpToHttpsEnabled"
              :disabled="loading.getConfiguration || loading.configureModule"
              class="mg-bottom"
            >
              <template slot="text-left">{{
                $t("settings.disabled")
              }}</template>
              <template slot="text-right">{{
                $t("settings.enabled")
              }}</template>
            </cv-toggle>

            <!-- Email/SMTP Settings -->
            <cv-accordion ref="accordion" class="maxwidth mg-bottom">
              <cv-accordion-item :open="toggleAccordion[0]">
                <template slot="title">{{ $t("settings.email_settings") }}</template>
                <template slot="content">
                  <cv-select
                    :label="$t('settings.smtp_transport')"
                    v-model="smtpTransport"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <cv-select-option value="smtp-auth">{{ $t("settings.smtp_auth") }}</cv-select-option>
                    <cv-select-option value="smtp-api">{{ $t("settings.smtp_api") }}</cv-select-option>
                    <cv-select-option value="resend">{{ $t("settings.resend") }}</cv-select-option>
                    <cv-select-option value="mailchannels">{{ $t("settings.mailchannels") }}</cv-select-option>
                  </cv-select>

                  <cv-text-input
                    v-if="smtpTransport === 'smtp-auth' || smtpTransport === 'smtp-api'"
                    :label="$t('settings.smtp_host')"
                    v-model="smtpHost"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    v-if="smtpTransport === 'smtp-auth' || smtpTransport === 'smtp-api'"
                    :label="$t('settings.smtp_port')"
                    v-model="smtpPort"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    v-if="smtpTransport === 'smtp-auth'"
                    :label="$t('settings.smtp_username')"
                    v-model="smtpUsername"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    v-if="smtpTransport === 'smtp-auth'"
                    :label="$t('settings.smtp_password')"
                    v-model="smtpPassword"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <cv-text-input
                    v-if="smtpTransport === 'resend'"
                    :label="$t('settings.resend_api_key')"
                    v-model="resendApiKey"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <cv-text-input
                    v-if="smtpTransport === 'mailchannels'"
                    :label="$t('settings.mailchannels_api_key')"
                    v-model="mailchannelsApiKey"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <cv-text-input
                    :label="$t('settings.smtp_from_name')"
                    v-model="smtpFromName"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    :label="$t('settings.smtp_from_address')"
                    v-model="smtpFromAddress"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-toggle
                    v-if="smtpTransport === 'smtp-auth'"
                    value="smtpSecure"
                    :label="$t('settings.smtp_secure')"
                    v-model="smtpSecure"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                </template>
              </cv-accordion-item>

              <!-- OAuth Settings -->
              <cv-accordion-item :open="toggleAccordion[1]">
                <template slot="title">{{ $t("settings.oauth_providers") }}</template>
                <template slot="content">
                  <div class="subsection-title">{{ $t("settings.google_oauth") }}</div>
                  <cv-text-input
                    :label="$t('settings.google_client_id')"
                    v-model="googleClientId"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    :label="$t('settings.google_client_secret')"
                    v-model="googleClientSecret"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <div class="subsection-title">{{ $t("settings.microsoft_oauth") }}</div>
                  <cv-text-input
                    :label="$t('settings.microsoft_client_id')"
                    v-model="microsoftClientId"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    :label="$t('settings.microsoft_client_secret')"
                    v-model="microsoftClientSecret"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <div class="subsection-title">{{ $t("settings.oidc_provider") }}</div>
                  <cv-text-input
                    :label="$t('settings.oidc_well_known')"
                    v-model="oidcWellKnown"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    placeholder="https://idp.example.com/.well-known/openid-configuration"
                  />
                  <cv-text-input
                    :label="$t('settings.oidc_client_id')"
                    v-model="oidcClientId"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                  <cv-text-input
                    :label="$t('settings.oidc_client_secret')"
                    v-model="oidcClientSecret"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                  <cv-text-input
                    :label="$t('settings.oidc_provider_label')"
                    v-model="oidcProviderLabel"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                </template>
              </cv-accordion-item>

              <!-- Storage Settings -->
              <cv-accordion-item :open="toggleAccordion[2]">
                <template slot="title">{{ $t("settings.storage_settings") }}</template>
                <template slot="content">
                  <cv-select
                    :label="$t('settings.upload_transport')"
                    v-model="uploadTransport"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <cv-select-option value="database">{{ $t("settings.database") }}</cv-select-option>
                    <cv-select-option value="s3">{{ $t("settings.s3") }}</cv-select-option>
                  </cv-select>
                  <template v-if="uploadTransport === 's3'">
                    <cv-text-input
                      :label="$t('settings.s3_endpoint')"
                      v-model="s3Endpoint"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                      placeholder="https://s3.amazonaws.com"
                    />
                    <cv-text-input
                      :label="$t('settings.s3_region')"
                      v-model="s3Region"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                    />
                    <cv-text-input
                      :label="$t('settings.s3_bucket')"
                      v-model="s3Bucket"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                    />
                    <cv-text-input
                      :label="$t('settings.s3_access_key_id')"
                      v-model="s3AccessKeyId"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                    />
                    <cv-text-input
                      :label="$t('settings.s3_secret_access_key')"
                      v-model="s3SecretAccessKey"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                      password-toggle-visible
                    />
                    <cv-toggle
                      value="s3ForcePathStyle"
                      :label="$t('settings.s3_force_path_style')"
                      v-model="s3ForcePathStyle"
                      :disabled="loading.getConfiguration || loading.configureModule"
                      class="mg-bottom"
                    >
                      <template slot="text-left">{{
                        $t("settings.disabled")
                      }}</template>
                      <template slot="text-right">{{
                        $t("settings.enabled")
                      }}</template>
                    </cv-toggle>
                  </template>
                  <cv-text-input
                    :label="$t('settings.document_size_limit')"
                    v-model="documentSizeUploadLimit"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    helper-text="MB"
                  />
                </template>
              </cv-accordion-item>

              <!-- Feature Flags -->
              <cv-accordion-item :open="toggleAccordion[3]">
                <template slot="title">{{ $t("settings.feature_flags") }}</template>
                <template slot="content">
                  <cv-toggle
                    value="disableSignup"
                    :label="$t('settings.disable_signup')"
                    v-model="disableSignup"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                  <cv-toggle
                    value="featureBillingEnabled"
                    :label="$t('settings.feature_billing')"
                    v-model="featureBillingEnabled"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                  <cv-toggle
                    value="disableTelemetry"
                    :label="$t('settings.disable_telemetry')"
                    v-model="disableTelemetry"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  >
                    <template slot="text-left">{{
                      $t("settings.disabled")
                    }}</template>
                    <template slot="text-right">{{
                      $t("settings.enabled")
                    }}</template>
                  </cv-toggle>
                  <cv-text-input
                    :label="$t('settings.posthog_key')"
                    v-model="posthogKey"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                  />
                </template>
              </cv-accordion-item>

              <!-- Signing Certificate -->
              <cv-accordion-item :open="toggleAccordion[4]">
                <template slot="title">{{ $t("settings.signing_settings") }}</template>
                <template slot="content">
                  <cv-text-input
                    :label="$t('settings.signing_certificate_path')"
                    v-model="signingLocalFilePath"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    helper-text="/opt/documenso/cert.p12"
                  />
                  <cv-text-input
                    :label="$t('settings.signing_passphrase')"
                    v-model="signingPassphrase"
                    :disabled="loading.getConfiguration || loading.configureModule"
                    class="mg-bottom"
                    password-toggle-visible
                  />
                </template>
              </cv-accordion-item>
            </cv-accordion>

            <cv-row v-if="error.configureModule">
              <cv-column>
                <NsInlineNotification
                  kind="error"
                  :title="$t('action.configure-module')"
                  :description="error.configureModule"
                  :showCloseButton="false"
                />
              </cv-column>
            </cv-row>
            <NsButton
              kind="primary"
              :icon="Save20"
              :loading="loading.configureModule"
              :disabled="loading.getConfiguration || loading.configureModule"
              >{{ $t("settings.save") }}</NsButton
            >
          </cv-form>
        </cv-tile>
      </cv-column>
    </cv-row>
  </cv-grid>
</template>

<script>
import to from "await-to-js";
import { mapState } from "vuex";
import {
  QueryParamService,
  UtilService,
  TaskService,
  IconService,
  PageTitleService,
} from "@nethserver/ns8-ui-lib";

export default {
  name: "Settings",
  mixins: [
    TaskService,
    IconService,
    UtilService,
    QueryParamService,
    PageTitleService,
  ],
  pageTitle() {
    return this.$t("settings.title") + " - " + this.appName;
  },
  data() {
    return {
      q: {
        page: "settings",
      },
      urlCheckInterval: null,
      host: "",
      isLetsEncryptEnabled: false,
      isHttpToHttpsEnabled: true,
      toggleAccordion: [false, false, false, false, false],
      loading: {
        getConfiguration: false,
        configureModule: false,
      },
      error: {
        getConfiguration: "",
        configureModule: "",
        host: "",
        lets_encrypt: "",
        http2https: "",
      },
      smtpTransport: "smtp-auth",
      smtpHost: "",
      smtpPort: "587",
      smtpUsername: "",
      smtpPassword: "",
      smtpFromName: "Documenso",
      smtpFromAddress: "",
      smtpSecure: false,
      resendApiKey: "",
      mailchannelsApiKey: "",
      googleClientId: "",
      googleClientSecret: "",
      microsoftClientId: "",
      microsoftClientSecret: "",
      oidcWellKnown: "",
      oidcClientId: "",
      oidcClientSecret: "",
      oidcProviderLabel: "OIDC",
      uploadTransport: "database",
      s3Endpoint: "",
      s3Region: "us-east-1",
      s3Bucket: "",
      s3AccessKeyId: "",
      s3SecretAccessKey: "",
      s3ForcePathStyle: false,
      documentSizeUploadLimit: "5",
      disableSignup: false,
      featureBillingEnabled: false,
      disableTelemetry: false,
      posthogKey: "",
      signingLocalFilePath: "/opt/documenso/cert.p12",
      signingPassphrase: "",
    };
  },
  computed: {
    ...mapState(["instanceName", "core", "appName"]),
  },
  created() {
    this.getConfiguration();
  },
  beforeRouteEnter(to, from, next) {
    next((vm) => {
      vm.watchQueryData(vm);
      vm.urlCheckInterval = vm.initUrlBindingForApp(vm, vm.q.page);
    });
  },
  beforeRouteLeave(to, from, next) {
    clearInterval(this.urlCheckInterval);
    next();
  },
  methods: {
    async getConfiguration() {
      this.loading.getConfiguration = true;
      this.error.getConfiguration = "";
      const taskAction = "get-configuration";
      const eventId = this.getUuid();

      this.core.$root.$once(
        `${taskAction}-aborted-${eventId}`,
        this.getConfigurationAborted
      );

      this.core.$root.$once(
        `${taskAction}-completed-${eventId}`,
        this.getConfigurationCompleted
      );

      const res = await to(
        this.createModuleTaskForApp(this.instanceName, {
          action: taskAction,
          extra: {
            title: this.$t("action." + taskAction),
            isNotificationHidden: true,
            eventId,
          },
        })
      );
      const err = res[0];

      if (err) {
        console.error(`error creating task ${taskAction}`, err);
        this.error.getConfiguration = this.getErrorMessage(err);
        this.loading.getConfiguration = false;
        return;
      }
    },
    getConfigurationAborted(taskResult, taskContext) {
      console.error(`${taskContext.action} aborted`, taskResult);
      this.error.getConfiguration = this.$t("error.generic_error");
      this.loading.getConfiguration = false;
    },
    getConfigurationCompleted(taskContext, taskResult) {
      const config = taskResult.output;
      this.host = config.host || "";
      this.isLetsEncryptEnabled = config.lets_encrypt || false;
      this.isHttpToHttpsEnabled = config.http2https !== false;

      this.smtpTransport = config.smtp_transport || "smtp-auth";
      this.smtpHost = config.smtp_host || "";
      this.smtpPort = String(config.smtp_port || "587");
      this.smtpUsername = config.smtp_username || "";
      this.smtpPassword = config.smtp_password || "";
      this.smtpFromName = config.smtp_from_name || "Documenso";
      this.smtpFromAddress = config.smtp_from_address || "";
      this.smtpSecure = config.smtp_secure === "true" || config.smtp_secure === true;
      this.resendApiKey = config.resend_api_key || "";
      this.mailchannelsApiKey = config.mailchannels_api_key || "";

      this.googleClientId = config.google_client_id || "";
      this.googleClientSecret = config.google_client_secret || "";
      this.microsoftClientId = config.microsoft_client_id || "";
      this.microsoftClientSecret = config.microsoft_client_secret || "";
      this.oidcWellKnown = config.oidc_well_known || "";
      this.oidcClientId = config.oidc_client_id || "";
      this.oidcClientSecret = config.oidc_client_secret || "";
      this.oidcProviderLabel = config.oidc_provider_label || "OIDC";

      this.uploadTransport = config.upload_transport || "database";
      this.s3Endpoint = config.s3_endpoint || "";
      this.s3Region = config.s3_region || "us-east-1";
      this.s3Bucket = config.s3_bucket || "";
      this.s3AccessKeyId = config.s3_access_key_id || "";
      this.s3SecretAccessKey = config.s3_secret_access_key || "";
      this.s3ForcePathStyle = config.s3_force_path_style === "true" || config.s3_force_path_style === true;
      this.documentSizeUploadLimit = String(config.document_size_upload_limit || "5");

      this.disableSignup = config.disable_signup === "true" || config.disable_signup === true;
      this.featureBillingEnabled = config.feature_billing_enabled === "true" || config.feature_billing_enabled === true;
      this.disableTelemetry = config.disable_telemetry === "true" || config.disable_telemetry === true;
      this.posthogKey = config.posthog_key || "";

      this.signingLocalFilePath = config.signing_local_file_path || "/opt/documenso/cert.p12";
      this.signingPassphrase = config.signing_passphrase || "";

      this.loading.getConfiguration = false;
      this.focusElement("host");
    },
    validateConfigureModule() {
      this.clearErrors(this);

      let isValidationOk = true;
      if (!this.host) {
        this.error.host = "common.required";

        if (isValidationOk) {
          this.focusElement("host");
        }
        isValidationOk = false;
      }
      return isValidationOk;
    },
    configureModuleValidationFailed(validationErrors) {
      this.loading.configureModule = false;
      let focusAlreadySet = false;

      for (const validationError of validationErrors) {
        const param = validationError.parameter;
        this.error[param] = this.$t("settings." + validationError.error);

        if (!focusAlreadySet) {
          this.focusElement(param);
          focusAlreadySet = true;
        }
      }
    },
    async configureModule() {
      this.error.test_imap = false;
      this.error.test_smtp = false;
      const isValidationOk = this.validateConfigureModule();
      if (!isValidationOk) {
        return;
      }

      this.loading.configureModule = true;
      const taskAction = "configure-module";
      const eventId = this.getUuid();

      this.core.$root.$once(
        `${taskAction}-aborted-${eventId}`,
        this.configureModuleAborted
      );

      this.core.$root.$once(
        `${taskAction}-validation-failed-${eventId}`,
        this.configureModuleValidationFailed
      );

      this.core.$root.$once(
        `${taskAction}-completed-${eventId}`,
        this.configureModuleCompleted
      );

      const configData = {
        host: this.host,
        lets_encrypt: this.isLetsEncryptEnabled,
        http2https: this.isHttpToHttpsEnabled,
        smtp_transport: this.smtpTransport,
        smtp_host: this.smtpHost,
        smtp_port: this.smtpPort,
        smtp_username: this.smtpUsername,
        smtp_password: this.smtpPassword,
        smtp_from_name: this.smtpFromName,
        smtp_from_address: this.smtpFromAddress,
        smtp_secure: String(this.smtpSecure),
        resend_api_key: this.resendApiKey,
        mailchannels_api_key: this.mailchannelsApiKey,
        google_client_id: this.googleClientId,
        google_client_secret: this.googleClientSecret,
        microsoft_client_id: this.microsoftClientId,
        microsoft_client_secret: this.microsoftClientSecret,
        oidc_well_known: this.oidcWellKnown,
        oidc_client_id: this.oidcClientId,
        oidc_client_secret: this.oidcClientSecret,
        oidc_provider_label: this.oidcProviderLabel,
        upload_transport: this.uploadTransport,
        s3_endpoint: this.s3Endpoint,
        s3_region: this.s3Region,
        s3_bucket: this.s3Bucket,
        s3_access_key_id: this.s3AccessKeyId,
        s3_secret_access_key: this.s3SecretAccessKey,
        s3_force_path_style: String(this.s3ForcePathStyle),
        document_size_upload_limit: this.documentSizeUploadLimit,
        disable_signup: String(this.disableSignup),
        feature_billing_enabled: String(this.featureBillingEnabled),
        disable_telemetry: String(this.disableTelemetry),
        posthog_key: this.posthogKey,
        signing_local_file_path: this.signingLocalFilePath,
        signing_passphrase: this.signingPassphrase,
      };

      const res = await to(
        this.createModuleTaskForApp(this.instanceName, {
          action: taskAction,
          data: configData,
          extra: {
            title: this.$t("settings.instance_configuration", {
              instance: this.instanceName,
            }),
            description: this.$t("settings.configuring"),
            eventId,
          },
        })
      );
      const err = res[0];

      if (err) {
        console.error(`error creating task ${taskAction}`, err);
        this.error.configureModule = this.getErrorMessage(err);
        this.loading.configureModule = false;
        return;
      }
    },
    configureModuleAborted(taskResult, taskContext) {
      console.error(`${taskContext.action} aborted`, taskResult);
      this.error.configureModule = this.$t("error.generic_error");
      this.loading.configureModule = false;
    },
    configureModuleCompleted() {
      this.loading.configureModule = false;
      this.getConfiguration();
    },
  },
};
</script>

<style scoped lang="scss">
@import "../styles/carbon-utils";
.mg-bottom {
  margin-bottom: $spacing-06;
}

.maxwidth {
  max-width: 38rem;
}

.section-title {
  font-size: 1.25rem;
  font-weight: 600;
  margin-bottom: $spacing-06;
  color: var(--cds-text-primary);
}

.subsection-title {
  font-size: 1rem;
  font-weight: 500;
  margin-top: $spacing-05;
  margin-bottom: $spacing-04;
  color: var(--cds-text-secondary);
}
</style>
