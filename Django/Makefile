#
# Google cloud
#

create-gcloud-secret:
	gcloud secrets create django-secrets --data-file .env.gcloud

update-gcloud-secret:
	gcloud secrets versions add django-secrets --data-file .env.gcloud

view-gcloud-secret:
	gcloud secrets versions access "latest" --secret "django-secrets"

deploy:
	python manage.py collectstatic --noinput
	gcloud app deploy --quiet


#
# Heroku
#

 heroku-reset-db:
	heroku pg:reset
	heroku run python manage.py migrate
	heroku run python manage.py createsuperuser --username=swasher --email=mr.swasher@gmail.com
	heroku run python manage.py collectstatic

restore:
	cat latest.heroku.dump | docker exec -i sith_newborn-db-1 pg_restore --verbose --clean --no-acl --no-owner -h localhost -U postgres -d postgres
