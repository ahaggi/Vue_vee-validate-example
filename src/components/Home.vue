<template>
  <form @submit="onSubmit">
    <div class="formContainer">

      <!-- Notice the use of bootstrap class is-invalid-->
      <!-- <div class="col-md-3"> -->

      <div class="form-floating ">
        <input
          class="form-control"
          id="floatingInput"
          name="email"
          placeholder="name@example.com"
          v-model="email"
          :class="{ 'is-invalid': errors.email && errors.email.length > 0 }"
        />
        <label for="floatingInput">Email address</label>

                <!-- Notice the use of bootstrap class invalid-feedback-->
        <div class="invalid-feedback">{{ errors.email }}</div>
      </div>

      <p>Password: <input name="password" v-model="password" type="password" /></p>
      <span>{{ errors.password }}</span>

      <button type="submit" :disabled="!meta.dirty || isSubmitting">
        Submit
      </button>
    </div>
  </form>

  <pre>
    meta {{ meta }}

    errors {{ errors }}
    isSubmitting {{ isSubmitting }}

  </pre>
</template>

<script>
import { useForm, useField } from "vee-validate";

import { watch } from "vue";

export default {
  setup() {
    // Define a validation schema
    const validationSchema = {
      email(value) {
        // NOTE: isSubmitting.value
        // To validate only after submittion, we can check the isSubmitting.value
        /**
         * Submission Behavior
         *    Before validation stage
         *      isSubmitting:true
         *      touched: true
         *    Validation stage
         *      pending:true
         *      Runs the validation function/schema/rule
         *        If there are errors then it will skip the next stage and update the validation state (meta, errors) for the form and fields
         *    After validation stage
         *      Calls the handleSubmit
         *      isSubmitting:false
         */
        if (!isSubmitting.value) return true;
        const re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
        let valid = re.test(String(value).toLowerCase());
        if (!valid) {
          // if not valid: return false or null or a costume errMsg
          // return false;
          return "The email value does not has a valid format";
        }
        return true;
      },
      password(value) {
        // validate ONLY after submittion
        if (!isSubmitting.value) return true;

        if (value && value.length >= 8) {
          return true;
        } else {
          return "invalid password!";
          // return false;
        }
      },
    };

    let initialValues = {
      // // We can set an intial values
      // email: 'initial value',
      // password: '123'
    };

    // Create a form context with the validation schema
    const {
      meta, // this will hold the form validity state
      errors, // the errMsgs that are returned from the functions inside "validationSchema"
      handleSubmit,
      isSubmitting, // to be used inside validatingfuncs inside "validationSchema", inorder to validate ONLY after submittion
      setFieldError,
      setErrors, // can be used to set err manually, for ex. unique email validation inside  "handleSubmit"
    } = useForm({
      validationSchema: validationSchema,
      initialValues,
    });

    const onSubmit = handleSubmit((values) => {
      let uniqueEmail = Math.random() > 0.5;
      if (!uniqueEmail) {
        setFieldError("email", "The email is already registered!");
      } else {
        console.log(values);
      }
    });

    // No need to define rules for fields
    const { value: email } = useField("email");
    const { value: password } = useField("password");

    watch(isSubmitting, (currentValue, oldValue) => {
      console.log(`isSubmitting was: ${oldValue}`);
      console.log(`isSubmitting become: ${currentValue}`);
    });

    return {
      email,
      password,
      errors,
      meta,
      onSubmit,
      isSubmitting,
    };
  },
};
</script>

<style scoped>
.formContainer {
  display: flex;
  flex-direction: column;
  align-items: center;
}
</style>
