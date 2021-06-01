# Vee-validate

Install  
`npm i vee-validate@next --save`


## **vee-validate makes use of two flavors to add validation to your forms.**

- The first approach is using higher-order components (HOC) to validate your fields, OR
- The second approach is using the composition API to add the validation logic into your existing components.

### using higher-order components (HOC) to validate your fields
Great for simple UI components and native HTML elements with custom styling.


### Using the composition API to add the validation logic
We will be using the following composition functions to validate your forms:  
`useField`: Creates a form field with its validation state, you will use this inside your custom input components.

    useField: takes the flwg args:
        name: MaybeRef<string>, 
        rules?: MaybeRef<RuleExpression<TValue>>, 
        opts ?: {
            initialValue?: MaybeRef<TValue>;
            validateOnValueUpdate: boolean;
            validateOnMount?: boolean;
            bails?: boolean;
            type?: string;
            valueProp?: MaybeRef<TValue>;
            checkedValue?: MaybeRef<TValue>;
            uncheckedValue?: MaybeRef<TValue>;
        }

    useForm: returns an obj with the flwg properties:  
            {
                idx,
                fid,
                name,
                label,
                value,
                meta,
                errors,
                errorMessage,
                type,
                checkedValue,
                uncheckedValue,
                checked,
                resetField,
                handleReset,
                validate,
                handleChange,
                handleBlur,
                handleInput,
                setValidationState,
                setTouched,
                setErrors;
            }





`useForm` allows you to group fields created by useField and aggregates their state. 

    useForm: takes an obj as args with the flwg properties:
        {
            validationSchema?: MaybeRef<Record<keyof TValues, GenericValidateFunction | string | Record<string, any>> | SchemaOf<TValues>>;
            initialValues?: MaybeRef<TValues>;
            initialErrors?: Record<keyof TValues, string | undefined>;
            initialTouched?: Record<keyof TValues, boolean>;
            validateOnMount?: boolean;
        }


    useForm: returns an obj with the flwg properties
        errors ,
        meta ,
        values ,
        isSubmitting ,
        submitCount ,
        validate ,
        validateField ,
        handleReset ,
        resetForm ,
        handleSubmit ,

        // the flwg properties is called FormActions
        submitForm ,
        setFieldError ,
        setErrors ,
        setFieldValue ,
        setValues ,
        setFieldTouched ,
        setTouched

**Validation Behavior**
By default vee-validate runs validation whenever the value ref changes whether it was bound by a v-model, You can disable that behavior by passing a validateOnValueUpdate option set to false.


**`meta`** contains useful information about the form, it acts as an aggregation of the metadata for the fields inside that form.

```js
    const { meta } = useForm({...});

    meta.value.dirty;
    meta.value.pending;
    meta.value.touched;
    meta.value.valid;
    meta.value.initialValues;
```

**Submission Behavior**  

    * Before validation stage  
        - isSubmitting:true  
        - touched: true  

    * Validation stage  
        - pending:true  
        - Runs the validation function/schema/rule  
            If there are errors then it will skip the next stage and update the validation state (meta, errors) for the form and fields  

    * After validation stage  
        - Calls the handleSubmit  
        - isSubmitting:false  

### To validate ONLY after submittion, we can check the isSubmitting.value




### validator function  

The validator function is a simple function that receives the current field value as the first argument, and it should return:
- true if the validation passed.  
- string if the validation failed and there is an error message to display.  
- false if validation fails, in that case, vee-validate will try to generate an error message using config.defaultMessage.  