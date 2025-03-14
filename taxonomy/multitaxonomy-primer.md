# Multiple Taxonomy Implementation Guide for ONDC

This guide explains how to implement multiple parallel taxonomy structures within a single ONDC catalog, giving buyer apps more flexibility in product navigation and discovery.

## Core Implementation Strategy

The implementation follows these key principles:

1. **Primary Taxonomy as Anchor**: The RET12-based taxonomy serves as the canonical reference
2. **Alternative Views**: Additional taxonomies provide different navigation paths to the same products
3. **Cross-Referencing**: Bidirectional mappings between taxonomies enable seamless switching

## Taxonomy Structure Breakdown

### 1. Primary Taxonomy (Standard ONDC)

The primary taxonomy follows the ONDC-sanctioned RET12 structure from the provided taxonomy document:

```
RET12 (Fashion)
  ├── RET12-FA (Fashion Accessories)
  │    ├── RET12-100 (Headwraps)
  │    └── RET12-101 (Cufflinks)
  ├── RET12-TW (Topwear)
  │    ├── RET12-126 (Lounge T-Shirts)
  │    └── RET12-134 (Shirts)
  └── RET12-FW (Footwear)
       └── RET12-181 (Sandals & Floaters)
```

This provides the canonical product categorization required by ONDC.

### 2. Gender-Based Taxonomy

A parallel taxonomy organized by gender provides an alternative browsing path:

```
GENDER-MEN (Men)
  ├── GENDER-MEN-APPAREL (Men's Apparel)
  │    └── GENDER-MEN-APPAREL-TOPS (Tops) → Maps to RET12-134, RET12-126
  └── GENDER-MEN-ACCESSORIES (Men's Accessories) → Maps to RET12-101

GENDER-WOMEN (Women)
  └── GENDER-WOMEN-ACCESSORIES (Women's Accessories) → Maps to RET12-100

GENDER-UNISEX (Unisex)
  └── GENDER-UNISEX-FOOTWEAR (Unisex Footwear) → Maps to RET12-181
```

### 3. Occasion-Based Taxonomy

Another taxonomy organized by usage occasion:

```
OCCASION-FORMAL (Formal) → Maps to RET12-134, RET12-101
OCCASION-CASUAL (Casual) → Maps to RET12-126, RET12-181
```

## Implementation Details

### Structure in the Catalog

The implementation uses ONDC's `bpp/categories` array to contain multiple top-level taxonomy structures:

```json
"bpp/categories": [
  {
    "id": "taxonomy-primary",
    "descriptor": {
      "name": "Primary Taxonomy"
    },
    "categories": [...]
  },
  {
    "id": "taxonomy-gender",
    "descriptor": {
      "name": "Gender-Based Taxonomy"
    },
    "categories": [...]
  },
  {
    "id": "taxonomy-occasion",
    "descriptor": {
      "name": "Occasion-Based Taxonomy"
    },
    "categories": [...]
  }
]
```

### Cross-Referencing Between Taxonomies

Two cross-referencing mechanisms ensure taxonomies can map to each other:

#### 1. Category-to-Primary Mapping

Each alternative taxonomy category includes tags that map to primary taxonomy codes:

```json
{
  "id": "GENDER-MEN-APPAREL-TOPS",
  "descriptor": {
    "name": "Tops"
  },
  "tags": [
    {
      "code": "mapping",
      "list": [
        {
          "code": "primary_taxonomy",
          "value": "RET12-134, RET12-126"
        }
      ]
    }
  ]
}
```

#### 2. Product-to-Alternative Mapping

Each product includes tags that reference its position in alternative taxonomies:

```json
{
  "tags": [
    {
      "code": "alternate_taxonomies",
      "list": [
        {
          "code": "gender_taxonomy", 
          "value": "GENDER-MEN-APPAREL-TOPS"
        },
        {
          "code": "occasion_taxonomy",
          "value": "OCCASION-FORMAL"
        }
      ]
    }
  ]
}
```

## Benefits for Buyer Apps

This multi-taxonomy approach provides several advantages for buyer applications:

1. **Multiple Navigation Paths**: Users can browse by product type, gender, occasion, or other facets
2. **Consistent Product Reference**: The RET12 code always serves as the canonical ID
3. **Flexible UI Options**: Buyer apps can display different taxonomy views based on user preference
4. **Improved Discovery**: Products become findable through multiple navigation paths
5. **ONDC Compliance**: Primary taxonomy maintains compatibility with ONDC standards

## Additional Taxonomy Options

Beyond the demonstrated examples, you could implement other taxonomies based on:

- **Price Range**: Economy, Premium, Luxury
- **Season**: Summer, Winter, Monsoon
- **Material**: Cotton, Silk, Leather, Synthetic
- **Brand**: Each brand as a top-level category
- **Trend**: Contemporary, Classic, Vintage

## Implementation Guidelines

When implementing multiple taxonomies:

1. **Keep the Primary Taxonomy Complete**: Ensure every product is properly categorized in the RET12 structure
2. **Limit Taxonomy Count**: 3-5 parallel taxonomies is typically sufficient
3. **Tag Products Consistently**: Ensure each product references all appropriate alternative taxonomies
4. **Document the Structure**: Provide clear documentation on how the taxonomies interrelate
5. **Consider API Performance**: More taxonomies mean larger catalog payloads

## Backward Compatibility

This implementation maintains full backward compatibility with ONDC:

- Buyer apps that only understand the primary taxonomy will function normally
- Legacy systems will still reference the canonical RET12 codes
- The enhanced structure is additive, not disruptive
