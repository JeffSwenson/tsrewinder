# Overview Of

```
laguage = SetupLanguageServices()
compiler = SetupCompilerServices()

for transformation in transformationsToRun():
    for file in project:
        ast = parse(file);
        edits = analyze(file, using languageService, compilerService, AST, and tsrewinder utilities)
        file.updateWithEdits(edits);
```

# Things to watch out for

* Running a transformation twice should produce the same output of running it once. This should allow the project the transformations are run on to reach a stable state.
* If a transformation is in a weird situation, its probably best not to do anything.
* Don't want to run on node_modules, .git, or any other none code objects.
* It is dangerous to change files without a backup. Could integrate with git and only run on committed or stashed files.

# Links
[Discussion of internal transforms used by the compiler](https://github.com/Microsoft/TypeScript/issues/5595)
[Discussion about editing AST](https://github.com/Microsoft/TypeScript/issues/1651)
[ntypescript, exposes the private compiler APIs](https://github.com/TypeStrong/ntypescript)

# Typescript compiler types

## Transformation
```
interface TransformationResult {
    transformed: SourceFile[];
    emitNodeWithSubstitution(emitContext: EmitContext, node: Node, emitCallback: (emitContext: EmitContext, node: Node) => void): void;
    emitNodeWithNotification(emitContext: EmitContext, node: Node, emitCallback: (emitContext: EmitContext, node: Node) => void): void;
}
interface TransformationContext extends LexicalEnvironment {
    getCompilerOptions(): CompilerOptions;
    getEmitResolver(): EmitResolver;
    getEmitHost(): EmitHost;
    hoistFunctionDeclaration(node: FunctionDeclaration): void;
    hoistVariableDeclaration(node: Identifier): void;
    enableSubstitution(kind: SyntaxKind): void;
    isSubstitutionEnabled(node: Node): boolean;
    onSubstituteNode?: (emitContext: EmitContext, node: Node) => Node;
    enableEmitNotification(kind: SyntaxKind): void;
    isEmitNotificationEnabled(node: Node): boolean;
    onEmitNode?: (emitContext: EmitContext, node: Node, emitCallback: (emitContext: EmitContext, node: Node) => void) => void;
}
type Transformer = (context: TransformationContext) => (node: SourceFile) => SourceFile;
function getTransformers(compilerOptions: CompilerOptions): Transformer[];
function transformFiles(resolver: EmitResolver, host: EmitHost, sourceFiles: SourceFile[], transformers: Transformer[]): TransformationResult;

interface ResolvedTypeReferenceDirective {
    primary: boolean;
    resolvedFileName?: string;
}
```
