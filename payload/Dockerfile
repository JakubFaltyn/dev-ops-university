FROM node:18.8-alpine as base

FROM base as builder

WORKDIR /home/node/app
COPY package*.json ./
COPY yarn.lock ./

# Install all dependencies including devDependencies
RUN yarn install --frozen-lockfile

COPY . .
RUN yarn build

FROM base as runtime

ENV NODE_ENV=production

WORKDIR /home/node/app
COPY --from=builder /home/node/app/dist ./dist
COPY --from=builder /home/node/app/package.json ./package.json
COPY --from=builder /home/node/app/yarn.lock ./yarn.lock

# Install only production dependencies
RUN yarn install --frozen-lockfile --production

EXPOSE 3000

CMD ["node", "dist/server.js"]
