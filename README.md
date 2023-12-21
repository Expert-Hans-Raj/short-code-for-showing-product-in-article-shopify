Task :- 1. Program a function to enter a SKU in the HTML view, see example below: [product="P-00095"]
4. Based on the SKU short-code, the desired item should be displayed in the blog post.
Comment:- Task is done,
Please follw the below instruction to implement this functionlity.

1. Create the shopify private app for getting the products and get the acess token form app.
2. This code is work for Dwan theme 12.0.0 theme, if this is not your Dwan theme got to theme code and open blog article template and add then 
 the "article-template__content" class on {{article.content}} element. 

 3. Add the mentioned jquery on theme.liqued file.
 Jquery CDN:-  <script src="https://code.jquery.com/jquery-3.6.4.min.js"></script>

4. create custom.js and paste my below script on this after then include this file to theme.liqued

<!-- getting product using jquery -->
<script>
$(document).ready(function () {
    const articleContent = document.querySelector('.article-template__content');
    const skuRegex = /\[product='([^']+)'\]/g; // Updated regex to match single quotes
    const skuNumbers = [];
    let match;

    while ((match = skuRegex.exec(articleContent.innerHTML)) !== null) {
        skuNumbers.push(match[1]);
    }

    // Now, skuNumbers array contains all SKU numbers from the article content
    console.log("Your skuNumbers: ", skuNumbers);

    var shopifyDomain = 'teststorehans.myshopify.com'; // replace with your Shopify store domain
    var storefrontAccessToken = 'f7391f12b8cbb5cd35df67a7b9ec6fd7'; // replace with your Storefront API access token

    var apiUrl = 'https://' + shopifyDomain + '/api/2021-07/graphql.json';

    // Function to generate HTML for a product variant
    function generateProductVariantHTML(product, sku) {
        // Find the matched variant based on SKU
        var matchedVariant = product.variants.edges.find(variant => variant.node.sku === sku);

        if (!matchedVariant) {
            // If no matched variant found, return an empty string
            return '';
        }

        return `<div class="variant-information">
                    <img src="${matchedVariant.node.image.originalSrc}" alt="${matchedVariant.node.image.altText}">
                    <div class="pro-short-code-info">
                        <h2 class="product-title">${product.title}</h2>
                        <p class="pro-price">${matchedVariant.node.priceV2.amount} ${matchedVariant.node.priceV2.currencyCode}</p>
                        <button class="add-to-cart-btn btn-bg" onclick="addToCart('${matchedVariant.node.id}', 1)">Add to Cart</button>
                    </div>
                </div>`;
    }

    // GraphQL query to retrieve products based on SKU numbers and all variants
    var query = `
        {
            products(first: 250) {
                edges {
                    node {
                        id
                        title
                        description
                        handle
                        variants(first: 10) {
                            edges {
                                node {
                                    id
                                    sku
                                    priceV2 {
                                        amount
                                        currencyCode
                                    }
                                    image {
                                        originalSrc
                                        altText
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
    `;

    $.ajax({
        type: 'POST',
        url: apiUrl,
        data: JSON.stringify({ query: query }),
        headers: {
            'X-Shopify-Storefront-Access-Token': storefrontAccessToken,
            'Content-Type': 'application/json',
        },
        success: function (response) {
            var products = response.data.products.edges;

            skuNumbers.forEach(sku => {
                // Filter products based on the current SKU
                var matchedProduct = products.find(product =>
                    product.node.variants.edges.some(variant => variant.node.sku === sku)
                );

                if (matchedProduct) {
                    // Generate HTML for the matched SKU variant of the product
                    var html = generateProductVariantHTML(matchedProduct.node, sku);

                    if (html) {
                        // Create a div element with the sku-number attribute and sku-item class
                        var skuDiv = document.createElement('div');
                        skuDiv.setAttribute('sku-number', sku);
                        skuDiv.classList.add('sku-item', 'mian-container-item');
                        skuDiv.innerHTML = html;

                        // Replace the shortcode with the div containing the product information for the matched variant
                        articleContent.innerHTML = articleContent.innerHTML.replace(`[product='${sku}']`, skuDiv.outerHTML);
                    }
                }
            });
        },
        error: function (error) {
            console.error('Error fetching products:', error);
        },
    });
});

// Function to simulate adding the product to the cart
function addToCart(productId, quantity) {
    // Extracting the last part of the productId
    const lastPart = productId.split('/').pop();
    const qty = 1;
    $.ajax({
        type: 'POST',
        url: "/cart/add.js",
        data: {
            id: lastPart,
            quantity: qty
        },
        dataType: 'json',
        success: function (response) {
            $.ajax({
                type: "GET",
                url: "/cart",
                dataType: "html",
                success: function (data) {
                    if ($(data).find(".error-message").length > 0) {
                        // Display an error message
                        alert("Product is out of stock. Please remove it from the cart.");
                    } else {
                        // Update the cart content
                        var newhtml = $(data).find(".cart__items").html();
                        var cartSubTotal = $(data).find(".totals__total-value").html();
                        $(".cart-notification-product").html(newhtml);
                        $(".drawer__inner-empty").html(newhtml);
                        $(".c-totals__total-value").html(cartSubTotal);
                        $(".cutom-cart").addClass("active");
                    }
                },
            });
        },
        error: function (error) {
            console.error('Error adding product to cart:', error);
            // You can handle the error here, e.g., show an error message to the user
        }
    });
}

5. Create css file and paste the code to css file after then include css file to theme.liqued
</script>


  <style>
    .variant-information {
    position: relative;
    border: 1px solid #e5e5e5;
    border-radius: 10px;
    overflow: hidden;
      margin-bottom: 12px;
}
    .variant-information img {
    border: 0;
}
    .variant-information .pro-short-code-info {
    padding: 0 20px 20px 20px;
    width: 100%;
}

     .btn-bg{
    background-color: #323232;
    color: #fff;
    border: 2px solid #0000;
    background-color: #323232;
    border-radius: 3px;
    letter-spacing: normal;
    color: #fff;
    text-transform: uppercase;
}
  </style>

<!--  getting product using jquery -->
 Email:- ecommerce.web.expert@gmail.com
