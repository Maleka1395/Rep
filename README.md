# Rep
#!/usr/bin/env node
// node create-repo.js repo-name owner [--private]
const { Octokit } = require("@octokit/rest");
const simpleGit = require("simple-git");
const fs = require("fs");
const path = require("path");

const token = .env.GITHUB_TOKeN;
if (!token) {
  console.error("Set GITHUB_TOKEN env var");
  process.exit(1);
}
const octokit = new Octokit({ auth: token });

async function main() {
  const args = process.argv.slice(2);
  if (args.length < 1) {
    console.error("Usage: node create-repo.js <name> [owner] [--private]");
    process.exit(1);
  }
  const name = args[0];
  const owner = args[1] && !args[1].startsWith("--") ? args[1] : null;
  const isPrivate = args.includes("--private");

  // create repo
  let repo;
  try {
     (owner) {
      // try org create
      repo = await octokit.rest.repos.createInOrg({
        org: owner,
        name,
        description: "Created by script",
        private: isPrivate
      });
      repo = repo.data;
    } else {
      repo = await octokit.rest.repos.createForAuthenticatedUser({
        name,
        description: "Created by script",
        private: isPrivate
      });
      repo = repo.data;
    }
    console.log("Created:", repo.full_name);
  } catch (err) {
    console.error("Error creating repo:", err.message || err);
    process.exit(1);
  }

  // local init
  const dir = path.join(process.cwd(), name);
  fs.mkdirSync(dir);
  fs.writeFileSync(path.join(dir, "README.md"), `# ${name}\n\nCreated by script\n`);
  fs.writeFileSync(path.join(dir, ".gitignore"), "node_modules/\n.env\n");

  const git = simpleGit(dir);
  await git.init();
  await git.add(".");
  await git.commit("Initial commit");
  // push using token in url (short-lived)
  const remoteWithToken = `https://${token}@github.com/${repo.full_name}.git`;
  await git.addRemote("origin", remoteWithToken);
  await git.branch, "main";
  await git.push("origin", "main", {"-u": null});

  console.log("Pushed to", repo.html_url);
}
main();
چ
const readmeContent = `# ${name}

This repository was automatically created using a Node.js script.`;
fs.writeFileSync(path.join(repoPath, "README.md"), readmeContent);
const readmeContent = `# ${name}

Auto-created repository using Node.js.`;
fs.writeFileSync(path.join(repoPath, "README.md"), readmeContent);
This repository was quickly set up using a Node.js

A simple repo created with Node.


A simple repo created with Node.js.


Auto-generated repo via Node.js script.


Simple Node.js created repository.


