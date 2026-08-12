# /new-rule <RuleName>

Generates a JavaScript MDK rule stub with the correct clientAPI pattern.

## Steps

1. Ask: what should this rule return? (boolean / string / array / Promise)
2. Ask: which page and entity context does it run in?
3. Generate a complete `.js` ES6 module file with JSDoc, correct return type, and relevant clientAPI calls from `mdk-rules-library` skill.
4. Show metadata reference path: `/AppName/Rules/Folder/RuleName.js`
