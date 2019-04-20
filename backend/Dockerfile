FROM python:3.6-alpine
ENV PYTHONUNBUFFERED 1
RUN addgroup -g 1000 -S gautam \
    && adduser -D -u 999 -G gautam gautam
# Creating working directory
RUN mkdir /code
WORKDIR /code
# Copying requirements
COPY . .
RUN apk add --no-cache --virtual .build-deps \
    ca-certificates gcc postgresql-dev linux-headers musl-dev \
    libffi-dev jpeg-dev zlib-dev  python3-dev \
    && python3 -m pip install -r requirements.txt --no-cache-dir \
    && find /usr/local \
        \( -type d -a -name test -o -name tests \) \
        -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
        -exec rm -rf '{}' + \
    && runDeps="$( \
        scanelf --needed --nobanner --recursive /usr/local \
                | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
                | sort -u \
                | xargs -r apk info --installed \
                | sort -u \
    )" \
    && apk add --virtual .rundeps $runDeps \
    && apk --purge del .build-deps

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]