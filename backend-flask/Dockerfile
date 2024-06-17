FROM python:3.10-slim-buster as base

WORKDIR /backend-flask

COPY . .

RUN pip3 install --no-cache-dir -r requirements.txt

##

FROM python:3.10-alpine3.20

WORKDIR /backend-flask

COPY --from=base /usr/local/lib/python3.10/site-packages/ /usr/local/lib/python3.10/site-packages/

COPY --from=base /backend-flask .

ENV FLASK_ENV=development

EXPOSE ${PORT}

RUN chmod +x docker_cmd.sh

CMD [ "sh", "docker_cmd.sh" ]