







## removeKeyword

### Ignore $ref
```
# Method #1
const Ajv = require('ajv')
const ajv = new Ajv({
    strict: false
})

ajv.removeKeyword('$ref')








# Method #2
const AJV = require('ajv').default
const ajv = new AJV()
const addFormats = require('ajv-formats').default


addFormats(ajv)

const mongodb = require('mongodb')
const ObjectId = id => id ? new mongodb.ObjectID(id) : new mongodb.ObjectID()
// const Ajv = require('ajv')
// const ajv = new Ajv()

function addMissingDefinitions(schema) {
    const definitions = {}

    function traverse(obj) {
        if (typeof obj === 'object' && obj !== null) {
            if ('$ref' in obj) {
                const ref = obj['$ref']
                const refName = ref.split('/').pop()
                if (!(refName in (schema.definitions || {})) && !(refName in definitions)) {
                    definitions[refName] = { type: 'object' }
                    obj['$ref'] = obj['$ref'].replace('#/', '#/definitions/')
                }
            } else {
                for (const key in obj) {
                    traverse(obj[key])
                }
            }
        }
    }

    traverse(schema)

    if (Object.keys(definitions).length > 0) {
        schema.definitions = schema.definitions || {}
        Object.assign(schema.definitions, definitions)
    }

    return schema
}


// Fucntion to validate a doc against the json schema
function validateDocumentAgainstSchema(doc, schema) {
    console.log('validateDocumentAgainstSchema() - doc', doc)
    console.log('validateDocumentAgainstSchema() - schema', schema)

    const updatedSchema = addMissingDefinitions(schema)
    console.log('validateDocumentAgainstSchema() - updatedSchema', updatedSchema)

    const validate = ajv.compile(updatedSchema)
    const isValid = validate(doc)

    if (!isValid) {
        const { errors } = validate
        throw new Error(JSON.stringify(errors, null, 4))
    }
}

```
