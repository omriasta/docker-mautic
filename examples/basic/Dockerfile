FROM mautic/mautic:7.0.1-apache

COPY ./themes /var/www/html/themes
COPY ./farmhouse.css /var/www/html/docroot/app/assets/css/
COPY ./FarmhouseRegular.woff2 /var/www/html/docroot/app/assets/fonts/
RUN apt -y install libavif15 
RUN apt -y install libxpm4 
RUN apt -y install nano

RUN a2enmod remoteip

#RUN sed -i "s|</VirtualHost>|<IfModule mod_remoteip.c>\n  RemoteIPHeader X-Forwarded-For\n  RemoteIPTrustedProxy 192.168.1.112 127.0.0.1\n</IfModule>\n</VirtualHost>|g" /etc/apache2/sites-available/000-default.conf

RUN composer update --working-dir=/var/www/html/
RUN composer require EMRL/mautic-twilio-sms-bundle --working-dir=/var/www/html
RUN sed -i 's/builder, array $data, string $formArea/builder, $data, $formArea/g' /var/www/html/docroot/plugins/TwilioSmsBundle/Integration/TwilioIntegration.php
RUN php /var/www/html/bin/console mautic:marketplace:install etailors/mautic-amazon-ses
RUN composer require firemultimedia/mautic-multi-captcha-bundle --working-dir=/var/www/html
RUN sed -i 's/"twilio\/sdk": "^5.25"/"twilio\/sdk": "^8.0.0"/g' /var/www/html/composer.lock
RUN composer require twilio/sdk:8.0.0 --working-dir=/var/www/html
RUN rm -r /var/www/html/var/cache/prod/*
RUN php /var/www/html/bin/console cache:clear
RUN sed -i 's/private array $metadata,/private $metadata,/g' /var/www/html/docroot/app/bundles/PluginBundle/Event/PluginUpdateEvent.php
#RUN php /var/www/html/bin/console mautic:plugins:update
#RUN sed -i 's/"twilio\/sdk": "^5.25"/"twilio\/sdk": "^8.0.0"/g' /var/www/html/composer.lock
#RUN composer require twilio/sdk:8.0.0 --working-dir=/var/www/html
#RUN composer require firemultimedia/mautic-multi-captcha-bundle --working-dir=/var/www/html
COPY ./TwilioTransport.php /var/www/html/docroot/app/bundles/SmsBundle/Integration/Twilio/TwilioTransport.php
RUN chown -R www-data:www-data /var/www/html
RUN cp /var/www/html/docroot/app/bundles/AssetBundle/Resources/views/Modules/preview.html.twig /var/www/html/docroot/app/bundles/AssetBundle/Resources/views/Asset/
