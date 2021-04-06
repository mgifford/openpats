# EARL + JSON-LD
The OpenPATS is an app created for accessibility auditors
to guide them in the process of creating accessibility
statements that align with the WCAG Evaluation Methodology. 
As any app should, the data is stored in a open standard format that 
can be saved and loaded back into the app.

At it's core, the OpenPATS Tool uses the Evaluation And
Report Language (EARL), developed by the W3C. EARL is build on
top of the Resource Description Framework (RDF), which means it
can be expressed using different formats such as XML, Turtle and
JSON-LD. The OpenPATS Tool requires significantly more
information than just the results of individual tests. It has
data about the evaluation itself, such as the scope, sample, title,
summary, etc. The EARL results are part of an evaluation, and are
therefore embedded within data like so:

```javascript
    {
        "dct:title": 'My OpenPATS Review',
        "scope": { /* EvaluationScope */ },
        // ...
        "auditResults": [ /* earl assertions */ ]
    }
```

## JSON Linked Data
Though RDF can be expressed in different formats, the OpenPATS Tool 
can only load evaluation data expressed as JSON-LD or YAML. 

# Classes
The OpenPATS Test format contains several classes. Most of them are
new to WCAG-EM, with a few of them are borrowed from other 
schemas and decorated with different properties. These classes and
the properties they are given in the OpenPATS Tool are described 
in the following sections.


## Namepaces
Several namespaces are used in the definition of the classes. 
The default namespace in a JSON-LD context is described with @vocab.
Any names, other than 'text' and 'URL', are part of this namespace.
The following namespaces are used:

- @vocab: (the default) http://www.w3.org/TR/WCAG-EM/#
- WCAG2: http://www.w3.org/TR/WCAG20/#
- dct: http://purl.org/dc/terms/
- sch: https://schema.org/
- earl: 'http://www.w3.org/ns/earl#

In addition to these namespaces, there are three possible values for
the WCAG 2 conformance target:
- level_a:   http://www.w3.org/WAI/WCAG2A-Conformance
- level_aa:  http://www.w3.org/WAI/WCAG2AA-Conformance
- level_aaa: http://www.w3.org/WAI/WCAG2AAA-Conformance

## Evaluation
An evaluation is the process of testing the accessibility of a digital tool
according to OpenPATS. Objects of type Evaluation contains the
data generated as part of the evaluation.

Special: The @language property of JSON-LD will be used by the Report
Tool to identify the language used in the evaluation.

### Evaluation properties
Property            | Type               | Description
--------------------|--------------------|----------------------------------
dct:title           | text               | Title of the evaluation report
dct:summary         | text               | Executive summary
dct:creator         | earl:Assertor      | Author and creator of the evaluation
dct:date            | text               | Completion date of the evaluation
commissioner        | text               | Person/organization who commissioned the evaluation
WCAG2:reliedupondef | WCAG2:technologydef| Web technologies the website relies upon for providing it's content
step1               | EvaluationScope    | Scope of the evaluation
step2a              | text               | Common pages of the website
step2b              | text               | Essential functionality of the website
step2c              | text               | Variety of page types of the website
step2e              | text               | Other relevant pages  of the website
step3a              | Sample             | Structured sample pages
step3b              | Sample             | Random sample pages
step4               | earl:Assertion     | Audit results of the evaluation
step5b              | text               | Evaluation specifics

### Example
```javascript
    {
        "@type":        "Evaluation",
        "@language":    "en",
        "dct:title":    "Evaluation of example.org",
        "dct:summary":  "This website is very accessible.",
        "dct:creator":  "John Doe",
        "dct:date":     "2008-12-11",
        "commissioner": "Example Inc.",
        "WCAG2:reliedupondef": [
            { /* WCAG2:technologydef 1 */ },
            { /* WCAG2:technologydef 2 */ }
        ],
        "step1":        { /* EvaluationScope */ },
        "step2a":       "Home, contact",
        "step2b":       "The web shop",
        "step2c":       "PDF, tables, video's",
        "step2e":       "Copyright page, Accessibility statement",
        "step3a":       { /* Sample (structured) */ },
        "step3b":       { /* Sample (random) */ },
        "step4": [
            { /* earl:Assertion 1 */ },
            { /* earl:Assertion 2 */ }
        ],
        "step5b":       "Results of the evaluation can be found at example.org/archive/123"
    }
```

## EvaluationScope - TODO: This doesn't align
Objects of type EvaluationScope described what should and what should
not be considered as part of the evaluation. This includes both a 
definition of the website that is to be tested, as well as the
exact requirements against which the website is tested.

### EvaluationScope properties - TODO: This doesn't align
Properties                | Type        | Description
--------------------------|-------------|----------------------------------
WCAG2:set-of-web-pagesdef | sch:WebSite | Website that is to be evaluated
step1b                    | URL (level) | Conformance target
step1c                    | text        | Accessibility support baseline
step1d                    | text        | Additional evaluation requirement

### Example - TODO: This doesn't align
```javascript
    {
        "@type": "EvaluationScope",
        "WCAG2:set-of-web-pagesdef": { /* WebSite */ },
        "step1b": "http://www.w3.org/WAI/WCAG2AA-Conformance",
        "step1c": "IE + Jaws. Firefox & Chrome + NVDA, Safari + VoiceOver",
        "step1d": "Including Level AAA success criteria of principle 1",
    }
```


### sch:WebPage properties - TODO: This doesn't align
Properties          | Type    | Description
--------------------|---------|------------------------------------------------------
@id                 | URL     | URL or internal ID to be reffered to from assertions
dct:title           | text    | Title for the webpabe
dct:description     | text    | Description of how to show the page
dct:source          | URL     | Source (url) of the web page
reporter:tested     | boolean | If the page is fully tested yet

### Example - TODO: This doesn't align
```javascript
{
    "@type": ['earl:TestSubject', "sch:WebPage"],
    "dct:title": "Example Home",
    "dct:description": "http://example.com/ (mobile version)",
    "dct:source": "http://example.com/",
    "reporter:tested": false,
}
```

## WCAG2:technologydef - TODO: This doesn't align
Mechanism for encoding instructions to be rendered, played or executed
by user agents.

### WCAG2:technologydef properties
Properties    | Type    | Description
--------------|---------|------------------------------------------
@id           | URL     | Referenc to the technology specification
dct:title     | text    | Title by which the technology is known

### Example
```javascript
    {
        "@type": "WCAG2:technologydef",
        "@id": "http://www.w3.org/TR/html5/",
        "dct:title": "HTML 5"
    }
```

## earl:Assertor
An assertor is an entity such as a person, a software tool, an organization,
or any other grouping that carries out a test collectively.
For more details see: http://www.w3.org/TR/EARL10-Schema/#Assertor

The WCAG-EM Report Tool fills in the `foaf:name` property of whichever
type was used. When creating an evaluation, the 'foaf:Person' class is used.


## earl:Assertion
Assertions are described according to EARL and will also include a TestResult.
For more details see: http://www.w3.org/TR/EARL10-Schema/#Assertion

Important note: To refer to WCAG 2 success criteria, the IDs of given to their
definition in the official WCAG 2 specification are used. For example, the 
definition of Success Criterion 1.1.1 is http://www.w3.org/TR/WCAG20/#text-equiv-all

### hasPart & earl:subject

The earl:subject is a reference to the @id of either a WebPage in either of the
Sample object, or the WebSite object which is part of the EvaluationScope. If it
is neither of these, they will not be included in the WCAG-EM Report Tool.

The use of 'dct:hasPart' to indicate a relationship between the assertions with
the website as it's subject, and the assertions with webpages as their subject is
*not* part of the EARL specification. The WCAG-EM Report Tool uses this 
relationship to determine how to group page assertions. Because all assertions
refer to a 'test' this relationship is redundant and will therefore be dropped
in a future version of the WCAG-EM Report Tool.

### Example
```javascript
    {
        "@type": "earl:Assertion",
        "earl:test": "WCAG2:text-equiv-all",
        "earl:assertedBy": "_:evaluator",
        "earl:subject": "_:website",
        "earl:result": {
          "@type": "earl:TestResult",
          "earl:outcome": "earl:failed",
          "dct:description": "Images must have a text alternative"
        },
        "earl:mode": "manual",
        "dct:hasPart": [
            { /* earl:Assertion for WebPage 1 */ },
            { /* earl:Assertion for WebPage 2 */ }
        ]
    }
```

## Tested

This boolean value is used by the Report Tool to track if software is tested.
