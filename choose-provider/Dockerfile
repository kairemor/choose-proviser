FROM --platform=${TARGETPLATFORM:-linux/amd64} openfaas/of-watchdog:0.7.7 as watchdog
FROM --platform=${TARGETPLATFORM:-linux/amd64} python:3.7-slim-buster

COPY --from=watchdog /fwatchdog /usr/bin/fwatchdog
RUN chmod +x /usr/bin/fwatchdog

# Uncomment if you want to use native modules
#RUN apt-get -qy update && apt-get -qy install gcc make

# Add non root user
RUN addgroup --system app && adduser app --system --ingroup app
RUN chown app /home/app

USER app
ENV PATH=$PATH:/home/app/.local/bin

WORKDIR /home/app/

COPY manage.py          .
COPY requirements.txt   .
COPY root_app		.
COPY api		.
COPY db.sqlite3		.

USER root
RUN pip install -r requirements.txt

WORKDIR /home/app/

RUN chown -R app:app *
USER app

ENV fprocess="diango-admin manage.py runserver"

ENV upstream_url="http://127.0.0.1:8000"
ENV mode="http"
ENV cgi_headers="true"

CMD ["fwatchdog"]
