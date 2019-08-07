# Start from the latest golang base image
FROM golang:alpine as builder

RUN apk --no-cache add git

# Set the Current Working Directory inside the container
WORKDIR /app

COPY haminer/ .

# Build the Go app
RUN go build -o parser ./cmd/haminer


######## Start a new stage from scratch #######
FROM alpine:latest  

COPY haminer.conf /etc/haminer.conf

WORKDIR /app

# Copy the Pre-built binary file from the previous stage
COPY --from=builder /app/parser .

# Command to run the executable
CMD ["./parser"] 