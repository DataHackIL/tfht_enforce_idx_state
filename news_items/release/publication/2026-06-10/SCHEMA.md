# news_items schema

| field | type | required |
|---|---|---|
| id | string | yes |
| source_name | string | yes |
| source_domain | string | yes |
| url | string | yes |
| canonical_url | string | yes |
| publication_datetime | string | yes |
| retrieval_datetime | string | yes |
| language | string | no |
| title | string | yes |
| taxonomy_version | {'type': 'string'}, {'type': 'null'} | no |
| taxonomy_category_id | {'type': 'string'}, {'type': 'null'} | no |
| taxonomy_subcategory_id | {'type': 'string'}, {'type': 'null'} | no |
| index_relevant | boolean | no |
| category | #/$defs/Category | yes |
| sub_category | {'$ref': '#/$defs/SubCategory'}, {'type': 'null'} | no |
| summary_one_sentence | string | yes |
| geography_country | string | no |
| geography_region | {'type': 'string'}, {'type': 'null'} | no |
| geography_city | {'type': 'string'}, {'type': 'null'} | no |
| manual_address | {'type': 'string'}, {'type': 'null'} | no |
| manual_event_label | {'type': 'string'}, {'type': 'null'} | no |
| manual_status | {'type': 'string'}, {'type': 'null'} | no |
| organizations_mentioned | array | no |
| topic_tags | array | no |
| rights_class | #/$defs/RightsClass | no |
| privacy_risk_level | #/$defs/PrivacyRisk | no |
| review_status | #/$defs/ReviewStatus | no |
| publication_status | #/$defs/PublicationStatus | no |
| takedown_status | #/$defs/TakedownStatus | no |
| manually_overridden | boolean | no |
| annotation_source | {'type': 'string'}, {'type': 'null'} | no |
| event_candidate_ids | array | no |
| created_at | string | no |
| updated_at | string | no |
| release_version | {'type': 'string'}, {'type': 'null'} | no |

Public exports are metadata-only and intentionally exclude article full text.
