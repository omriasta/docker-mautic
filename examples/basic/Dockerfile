FROM mautic/mautic:7.1.2-apache

# 1. System dependencies and Apache configuration
RUN apt-get update && apt-get install -y --no-install-recommends \
    libavif15 \
    libxpm4 \
    nano \
    && rm -rf /var/lib/apt/lists/* \
    && a2enmod remoteip

# 2. Handle Composer dependencies and resolve Twilio SDK conflict using an Alias
# This allows version 8.0.0 to satisfy requirements for version 5.x
RUN composer require \
    "twilio/sdk:8.0.0 as 5.42.2" \
    EMRL/mautic-twilio-sms-bundle \
    firemultimedia/mautic-multi-captcha-bundle \
    --working-dir=/var/www/html --no-interaction --optimize-autoloader \
    && php /var/www/html/bin/console mautic:marketplace:install etailors/mautic-amazon-ses \
    && sed -i 's/builder, array $data, string $formArea/builder, $data, $formArea/g' /var/www/html/docroot/plugins/TwilioSmsBundle/Integration/TwilioIntegration.php \
    && sed -i 's/private array $metadata,/private $metadata,/g' /var/www/html/docroot/app/bundles/PluginBundle/Event/PluginUpdateEvent.php

# 3. Copy assets with correct ownership
COPY --chown=www-data:www-data ./themes /var/www/html/themes
COPY --chown=www-data:www-data ./farmhouse.css /var/www/html/docroot/app/assets/css/
COPY --chown=www-data:www-data ./FarmhouseRegular.woff2 /var/www/html/docroot/app/assets/fonts/
COPY ./TwilioTransport.php /var/www/html/docroot/app/bundles/SmsBundle/Integration/Twilio/TwilioTransport.php

# 4. Finalize file system and clear cache
RUN cp /var/www/html/docroot/app/bundles/AssetBundle/Resources/views/Modules/preview.html.twig /var/www/html/docroot/app/bundles/AssetBundle/Resources/views/Asset/ \
    && rm -rf /var/www/html/var/cache/prod/* \
    && php /var/www/html/bin/console cache:clear \
    && chown -R www-data:www-data /var/www/html
