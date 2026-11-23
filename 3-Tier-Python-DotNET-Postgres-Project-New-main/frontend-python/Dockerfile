# syntax=docker/dockerfile:1

# ============================
# Stage 1 - Dependencies
# ============================

FROM python:3.13-slim AS builder

ENV VIRTUAL_ENV=/opt/venv

RUN python -m venv ${VIRTUAL_ENV}

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"

WORKDIR /build

COPY requirements.txt .

RUN pip install \
      --no-cache-dir \
      --upgrade pip \
    && pip install \
      --no-cache-dir \
      -r requirements.txt


# ============================
# Stage 2 - Runtime
# ============================

FROM python:3.13-slim AS runtime

ENV PYTHONDONTWRITEBYTECODE=1

ENV PYTHONUNBUFFERED=1

ENV VIRTUAL_ENV=/opt/venv

ENV PATH="${VIRTUAL_ENV}/bin:${PATH}"


RUN groupadd \
      --gid 10001 \
      appgroup \
    && useradd \
      --uid 10001 \
      --gid appgroup \
      --no-create-home \
      --shell /usr/sbin/nologin \
      appuser


WORKDIR /app


COPY --from=builder \
     --chown=appuser:appgroup \
     /opt/venv \
     /opt/venv


COPY --chown=appuser:appgroup \
     . \
     .


USER 10001:10001


EXPOSE 5000


CMD ["gunicorn", "--bind", "0.0.0.0:5000", "--workers", "2", "--threads", "4", "--access-logfile", "-", "--error-logfile", "-", "app:app"]
